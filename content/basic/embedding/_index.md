---
title: Embedding
---

One of the major features of Wired.IO when compared with ASP.NET Core is the possibility of embdedding it in any existing .NET application from console apps to Winforms, WPF, MAUI, AvaloniaUI you name it.

Wired.IO can easily be integrated into the existing app’s IServiceCollection, resolving services directly from it.

You can find below a practical example embedding Wired.IO into a .NET MAUI Application, allowing fully integration of a webserver and your UI like having your UI elements reacting to http requests.

This capability enables your app to automatically react to any incoming http requests from either external machines or other applications running in the same machine.


```csharp
using CommunityToolkit.Mvvm.ComponentModel;
using Microsoft.Extensions.Logging;
using Wired.IO.App;
using Wired.IO.Protocol.Response;

namespace EmbeddedWired;

public static class MauiProgram
{
	public static MauiApp CreateMauiApp()
	{
		var builder = MauiApp.CreateBuilder();
		builder
			.UseMauiApp<App>()
			.ConfigureFonts(fonts =>
			{
				fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
				fonts.AddFont("OpenSans-Semibold.ttf", "OpenSansSemibold");
			});

#if DEBUG
		builder.Logging.AddDebug();
#endif

        // Register the MainPage and MainViewModel,
        // Note: MainPage BindingContext is set to MainViewModel
		builder.Services.AddSingleton<MainPage>();
		builder.Services.AddSingleton<MainViewModel>();

        var wiredBuilder = WiredApp

            // Default Wired Http/1.1 builder
            .CreateExpressBuilder()
            .Port(8080)

            // Embedd your existing IServiceCollection
            .EmbedServices(builder.Services)

            .UseRootEndpoints()
            .MapGet("/route", context =>
            {
				var mainViewModel = scope.GetRequiredService<MainViewModel>();
				mainViewModel.Title = "Embedded Wired - Route";

                // Send response here
                context
                    .Respond()
                    .Status(ResponseStatus.Ok)
                    .Content(new ExpressStringContent("UI updated"))
                    .Type("plain/text"u8);
            });

        var mauiApp = builder.Build();

        var wiredApp = wiredBuilder.Build(mauiApp.Services);
        wiredApp.Start();

        return mauiApp;
    }
}

public partial class MainViewModel : ObservableObject
{
    [ObservableProperty]
    private string _title = "Embedded Wired";
    [ObservableProperty]
    private string _message = "Welcome to Embedded Wired!";
}
``