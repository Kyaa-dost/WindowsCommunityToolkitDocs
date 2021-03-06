---
title: Authentication Providers Overview
author: shweaver-MSFT
description: Authentication providers that enable easy access to Microsoft Graph APIs.
keywords: uwp, wpf, netstandard, windows, community, toolkit, graph, login, authentication, provider, providers, identity, msa, wam
dev_langs:
  - csharp
---

# Authentication Providers Overview

Authentication is always the first step to working with Microsoft Graph.
The toolkit offers a lightweight system for authenticating users, managing access tokens, and making Graph calls.

> [!IMPORTANT]
> Windows Community Toolkit - Graph Controls and Helpers packages are in preview. To get started using WCT preview packages visit the [WCT Preview Packages wiki page](https://aka.ms/wct/wiki/previewpackages).

## ProviderManager and the GlobalProvider

The [ProviderManager](./ProviderManager.md) is the singleton that stores the globally accessible [IProvider](./IProvider.md) implementation and signals events in response to authentication state changes.
Set the `GlobalProvider` property at app startup and any other Graph based code will respond to any changes as users login and logout.

```csharp
using CommunityToolkit.Authentication;

// Set the GlobalProvider to an IProvider implementation.
ProviderManager.Instance.GlobalProvider = new WindowsPRovider();
```

Invoke the ProviderManager anywhere you need to make Graph calls.
Listen for State changes on the GlobalProvider using the `ProviderChanged` event.

```csharp
using CommunityToolkit.Authentication;

ProviderManager.Instance.GlobalProvider.ProviderChanged += OnProviderChanged;

void OnProviderChanged (object sender, ProviderUpdatedEventArgs args)
{
    // Check the State property of the GlobalProvider to determine if a user is signed in or not.
    if (ProviderManager.Instance.GlobalProvider?.State == ProviderState.SignedIn)
    {
        // Signed in.
        // You can now make Graph calls.
    }
    else
    {
        // Signed out or loading.
        // Graph calls will fail.
    }
}
```

### MsalProvider

An [IProvider](./IProvider.md) implementation built on the official Microsoft Authentication Library (MSAL).
The [MsalProvider](./MsalProvider.md)  is NetStandard and supports any NetStandard 2.0 applications.

Available in the `CommunityToolkit.Authentication.Msal` package.

### WindowsProvider

A lightweight [IProvider](./IProvider.md) implementation built directly on the native Windows Account Manager (WAM) APIs.
The [WindowsProvider](./WindowsProvider.md) enables authentication of consumer MSA accounts without any Azure AAD config and only requires a Microsoft Store App registration to use.

Available in the `CommunityToolkit.Authentication.Uwp` package.

## Call Microsoft Graph APIs

Once authenticated, you can now make API calls to Microsoft Graph using a preconfigured GraphServiceClient.
Access to the client is enabled through an extension method on IProvider called, `GetClient()`.
See [ProviderExtensions](../helpers/ProviderExtensions.md) for more details.

Available in the `CommunityToolkit.Graph` package.

```csharp
using CommunityToolkit.Authentication;
using CommunityToolkit.Graph.Extensions;

IProvider provider = ProviderManager.Instance.GlobalProvider;
GraphServiceClient graphClient = provider.GetClient();

var me = await graphClient.Me.Request().GetAsync();
```
