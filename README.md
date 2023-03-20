# try-blazorwasm-standalone-singleOrg

| Status | Site URL |
|--|--|
| [![Deploy to GitHub Pages](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/cd-ghpages.yml/badge.svg?branch=main)](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/cd-ghpages.yml) | https://maremare.github.io/try-blazorwasm-standalone-singleOrg <br> https://wasm.trypage.tk |
| [![Azure Static Web Apps CI/CD](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/azure-static-web-apps-polite-moss-0d2a72510.yml/badge.svg?branch=main)](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/azure-static-web-apps-polite-moss-0d2a72510.yml) | https://polite-moss-0d2a72510.2.azurestaticapps.net |
| ![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=Cloudflare&logoColor=white) | https://try-blazorwasm-standalone-singleorg.pages.dev <br> https://wasm3.trypage.tk |

## `'Cannot read properties of undefined (reading 'toLowerCase')'`

GitHub Pages あるいは Azure Static Web Apps へ発行し、実際にサイトへアクセスすると次のエラーが表示される。
```
There was an error trying to log you in: 'Cannot read properties of undefined (reading 'toLowerCase')'
```

対処方法は *.csproj に以下を追加すれば良いらしい。
```xml
<ItemGroup>
  <TrimmerRootAssembly Include="Microsoft.Authentication.WebAssembly.Msal" />
</ItemGroup>
```

詳細は以下：
* https://github.com/dotnet/aspnetcore/issues/38082#issuecomment-1072762015

## `'"undefined" is not valid JSON'`
デプロイしたサイトへアクセスすると次のエラーが表示される。
```
There was an error trying to log you in: '"undefined" is not valid JSON'
```

対処方法は *.csproj に以下を追加すれば良いらしい。
```xml
<ItemGroup>
  <TrimmerRootAssembly Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" />
</ItemGroup>
```

詳細は以下：
* https://github.com/dotnet/aspnetcore/issues/44981#issue-1442616167
* https://github.com/dotnet/aspnetcore/issues/43293#issuecomment-1306046181

## リダイレクトの設定

<details>
<summary>詳細：</summary>

![](assets/AAD-redirect-setting.png)

</details>

## クライアントシークレットの設定
ユーザシークレットには以下のキーでクライアントシークレットを設定しているので、発行先の環境変数へ追加する必要がある。
```json
{
  "AzureAD:ClientSecret": "<<Client Secret>>"
}
```
### GitHub Pages

<details>
<summary>詳細：</summary>

![](assets/gh-pages-environment-secrets.png)

</details>

### Azure Static Web Apps

<details>
<summary>無料版ではダメだった：</summary>

![](assets/static-web-app-configuration.png)

![](assets/AADSTS900043.png)

[Azure AD 認証と承認のエラー コード \- Microsoft Entra \| Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/active-directory/develop/reference-aadsts-error-codes)

* AADSTS90043	
    > NationalCloudAuthCodeRedirection - 機能が無効になっています。


もしかして無料のホスティングプランだと設定できない？

![](assets/static-web-app-404.png)

以下が参考になりそう…

1. [Azure Static Web Apps の認証と承認 \| Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/static-web-apps/authentication-authorization?tabs=invitations) （マネージド認証）
    > 1 構成済みの Azure Active Directory プロバイダーは、Microsoft アカウントでサインインを許可します。
    > 
    > 特定の Active Directory テナントにログインを制限するには、[カスタム Azure Active Directory プロバイダー] を構成します。
2. [Azure Static Web Apps でのカスタム認証 \| Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/static-web-apps/authentication-custom?tabs=aad) （カスタム認証）
    > カスタム認証は、Azure Static Web Apps Standard プランでのみ使用できます。
3. [Azure Static Web AppsのアプリにAzure ADカスタム認証機能を追加](https://itc-engineering-blog.netlify.app/blogs/azure-static-web-apps-auth)
    > ## マネージド認証
    > > Azure AD のどのテナントでもログインできてしまいます。
    
    やりたいことは「カスタム認証」が適している。が、しかし無料が良かった…
    ここで断念。

</details>

### Cloudflare Pages
[Deploy a Blazor Site · Cloudflare Pages docs](https://developers.cloudflare.com/pages/framework-guides/deploy-a-blazor-site/#creating-the-build-script)

<details>
<summary>詳細：</summary>

![](assets/cloudflare-pages-config.png)

</details>

* ビルドの構成
  * ビルドコマンド
    ```sh
    curl -sSL https://dot.net/v1/dotnet-install.sh > dotnet-install.sh;
    chmod +x dotnet-install.sh;
    ./dotnet-install.sh -c 7.0 -InstallDir ./dotnet7;
    ./dotnet7/dotnet --version;
    ./dotnet7/dotnet publish "src/blazorwasm-standalone-singleOrg" -c Release -o output;
    ```
  * ビルド出力ディレクトリ
    ```sh
    /output/wwwroot
    ```

## `Microsoft.Graph v4 to v5`
Microsoft.Graph Version="4.54.0" → Microsoft.Graph Version="5.1.0" は破壊的変更がある模様。

### 以下が参考になりそう：
* [Microsoft Graph \.NET SDK v5 changelog and upgrade guide](https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/feature/5.0/docs/upgrade-to-v5.md)
* [Upgrade 4\.50 sdk to 5\.0 \- Reference to type IAuthenticationProvider claims it is defined in Microsoft\.Graph\.Core, but cannot be found · Issue \#1695 · microsoftgraph/msgraph\-sdk\-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/1695)
  * https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/1695#issuecomment-1464520102
* https://github.com/AzureAD/microsoft-identity-web/issues/2097
* https://github.com/microsoftgraph/msgraph-sample-aspnet-core/issues/84

### 一時的な解決方法？：
* https://github.com/AzureAD/microsoft-identity-web/issues/2097#issuecomment-1451707046
  * https://gist.github.com/ashelopukho/5b00944c7744ebb4f9baa348e86f7e0e
* https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/1695#issuecomment-1461759018
  * https://github.com/svrooij/BlazorGraphExplorer/commit/ab989ff959883f43e7ead10ff4e3c506022dbf33

## Breaking changes Authentication in WebAssembly apps
![image](https://user-images.githubusercontent.com/807378/226430419-706da0c0-3dd9-42c1-b12d-63cd50378182.png)
* https://github.com/dotnet/aspnetcore/issues/44973
  * https://github.com/dotnet/AspNetCore.Docs/pull/27562
  * https://github.com/dotnet/AspNetCore.Docs/blob/cfc5e4436eaeb090d3bfe55445285952aad7f07e/aspnetcore/blazor/security/includes/redirecttologin-component.md
  * https://learn.microsoft.com/en-us/dotnet/core/compatibility/aspnet-core/7.0/wasm-app-authentication

```cs
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
↓
```cs
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Options

@inject IOptionsSnapshot<RemoteAuthenticationOptions<ApiAuthorizationProviderOptions>> Options
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateToLogin(Options.Get(Microsoft.Extensions.Options.Options.DefaultName).AuthenticationPaths.LogInPath);
    }
}
```
