Multilingual Support and Localisation in ASP.NET Core Web Application with Razor Pages

I starts by configuring the application to support multiple languages(Mainly English and Bangla). This involves adding a resources folder and configuring the localization services in the program.cs file. The video then demonstrates creating a new Razor Page that allows the user to change the language. This page includes code to read the requested language from the URL and update a cookie to remember the user's preferred language.

Next, add a dropdown list to the navigation bar of the application to allow the user to change the language. The dropdown list includes options for the supported languages, which are English and Bangla. When a user selects a language from the dropdown list, they are redirected to the previous page with the appropriate culture parameter in the URL.

Then add multi-language support to the different pages in the application. This involves injecting the IViewLocalizer service into the view imports file and using it to access resources for the current language. Create resource files for the layout file and the home and privacy page. These resource files contain key-value pairs, where the key is the name of the resource and the value is the text that should be displayed in that language.

Finally, test the application. The application is initially displayed in English, but the user can change the language to Bangla using the dropdown list in the navigation bar. When the user selects Ban(Bangla), the page is displayed in Bangla.


******************************************************* Program.cs: *******************************************************


using Microsoft.AspNetCore.Mvc.Razor;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddLocalization(options => options.ResourcesPath = "Resources");

// Add services to the container.
builder.Services.AddRazorPages()
    .AddViewLocalization(LanguageViewLocationExpanderFormat.Suffix)
    .AddDataAnnotationsLocalization();

var app = builder.Build();

var supportedCultures = new[] { "en", "fr" };
var localizationOptions = new RequestLocalizationOptions().SetDefaultCulture(supportedCultures[0])
    .AddSupportedCultures(supportedCultures)
    .AddSupportedUICultures(supportedCultures);

app.UseRequestLocalization(localizationOptions);

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
}
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapRazorPages();

app.Run();



****************************************************** Lang.cshtml.cs ****************************************************





public class LangModel : PageModel
    {
        public void OnGet()
        {
            string? culture = Request.Query["culture"];
            Console.WriteLine("new selected language: " + culture);
            if (culture != null)
            {
                Response.Cookies.Append(
                    CookieRequestCultureProvider.DefaultCookieName,
                    CookieRequestCultureProvider.MakeCookieValue(new RequestCulture(culture)),
                    new CookieOptions { Expires = DateTimeOffset.UtcNow.AddYears(1) }
                );
            }


            string returnUrl = Request.Headers["Referer"].ToString() ?? "/";
            Response.Redirect(returnUrl);
        }
    }


**************************************************** _ViewImports.chstml *************************************************




@using WebApplication4
@namespace WebApplication4.Pages
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers

@using Microsoft.AspNetCore.Mvc.Localization
@inject IViewLocalizer Localizer


****************************************************** _Layout.chstml ****************************************************




<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - MultiLingualApp</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    <link rel="stylesheet" href="~/MultiLingualApp.styles.css" asp-append-version="true" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container">
                <a class="navbar-brand" asp-area="" asp-page="/Index">MultiLingualApp</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target=".navbar-collapse" aria-controls="navbarSupportedContent"
                        aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-page="/Index">@Localizer["home"]</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-page="/Privacy">@Localizer["privacy"]</a>
                        </li>
                    </ul>

                    <ul class="navbar-nav">
                        <li class="nav-item dropdown">
                            <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                                @Thread.CurrentThread.CurrentUICulture.Name
                            </a>
                  
                            <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
                                <li><a class="dropdown-item" href="/Lang?culture=en">En</a></li>
                                <li><a class="dropdown-item" href="/Lang?culture=bn">Ban</a></li>
                            </ul>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    </header>
    <div class="container">
        <main role="main" class="pb-3">
            @RenderBody()
        </main>
    </div>

    <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2024 - MultiLingualApp - <a asp-area="" asp-page="/Privacy">Privacy</a>
        </div>
    </footer>

    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>

    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>

**************************************** Screenshort of Privacy English Resource ****************************************

![Privacy en resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/35f0a236-b395-4b2d-a283-d1ddbac3d09a)

**************************************** Screenshort of Privacy Bangla Resource *****************************************

![Privacy bn resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/cc444be9-8cf8-400f-a14a-5185217260e5)

**************************************** Screenshort of Index English Resource ******************************************

![Index en resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/649fb38b-d1a6-44f8-aa2e-937ef7575eee)

**************************************** Screenshort of Index Bangla Resource ******************************************

![Index bn resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/d9bcac49-0139-4af0-a25f-2cae13c286ee)

**************************************** Screenshort of Layout English Resource *****************************************

![_Layout en resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/76746626-25fa-4b6d-9299-79ff97a56594)

**************************************** Screenshort of Layout Bangla Resource *****************************************

![_Layout bn resx](https://github.com/Rizve1503/Multilingual-Support-and-Localisation/assets/125442347/e562c132-2d2e-4657-a9d3-bef8d6de41f2)

