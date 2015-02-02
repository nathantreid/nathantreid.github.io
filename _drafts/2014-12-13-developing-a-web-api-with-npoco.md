---
title: Developing a .NET Web API application with NPoco
---

Step 1. Setting up the project
===
1. Create a new Console Application.
1. Add to source control.
1. Add the following packages:
  1. Microsoft AspNet Web API Owin Self Host, to supply the web api library.
  1. structuremap, our DI / IoC library.
  1. NPoco, the micro-ORM that provides us database access.
  1. MySQL.Data, since this example uses MySQL for the back-end database.

  Extras:
  1. SmartFormat.NET, to make formatting strings nice and simple.
  1. Topshelf, so the app can easily be run from the command-line or as a Windows service.
1. Set up dependency injection.
::::
  1. Add a ServiceActivator class to handle controller injection:
public class ServiceActivator : IHttpControllerActivator
{
    public ServiceActivator(HttpConfiguration configuration) {}

    public IHttpController Create(HttpRequestMessage request
        , HttpControllerDescriptor controllerDescriptor, Type controllerType)
    {
        var controller = ObjectFactory.GetInstance(controllerType) as IHttpController;
        return controller;
    }
}

::::
  1. Add a WebServer class to be our web server:
  using System.Web.Http;
using System.Web.Http.Dispatcher;
using Microsoft.Owin.Hosting;
using Owin;

namespace web_api_and_npoco_example_app.Infrastructure
{

    public class WebServer
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            var config = new HttpConfiguration();
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            config.Services.Replace(typeof(IHttpControllerActivator), new ServiceActivator(config));

            appBuilder.Run((owinContext) =>
            {
                owinContext.Response.ContentType = "text/plain";
                return owinContext.Response.WriteAsync("Api is available.");
            });
        }

        public void Start()
        {
            const string baseUrl = "http://*:5000/";

            WebApp.Start<WebServer>(baseUrl);

        }
    }
}

::::
  1. Update Program.cs to ensure only one instance of the app runs, configure Topshelf, and launch the webserver.
  using System;
using System.Threading;
using SmartFormat;
using Topshelf;
using web_api_and_npoco_example_app.Infrastructure;

namespace web_api_and_npoco_example_app
{
    class Program
    {
        private static readonly Mutex AppMutex = new Mutex(true, @"Global\e9e62c76-84e7-4156-9cbb-e88e61972855");
        private const string AppName = "Web API and NPoco Example App";
        static void Main(string[] args)
        {
            if (!AppMutex.WaitOne(0, true))
            {
                Console.WriteLine(Smart.Format("{AppName} is already running.", new { AppName }));
                return;
            }

            LaunchService();
        }

        static void LaunchService()
        {
            HostFactory.Run(x =>
            {
                x.Service<WebServer>(s =>
                {
                    s.ConstructUsing(name => new WebServer());
                    s.WhenStarted(svc => svc.Start());
                    s.WhenStopped(svc => Console.WriteLine("Goodbye!"));
                });

                x.SetDisplayName(AppName);
                x.SetServiceName(AppName.Replace(' ', '-'));
            });
        }
    }
}

1. At this point, you should be able to run the program and browse to http://localhost:5000/

Step 2. Set up database

Step 3. Set up
