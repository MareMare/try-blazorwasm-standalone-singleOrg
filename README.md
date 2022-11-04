# try-blazorwasm-standalone-singleOrg

| Status | Site URL |
|--|--|
| [![Deploy to GitHub Pages](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/cd-ghpages.yml/badge.svg?branch=main)](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/cd-ghpages.yml) | https://maremare.github.io/try-blazorwasm-standalone-singleOrg/ |
| [![Azure Static Web Apps CI/CD](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/azure-static-web-apps-polite-moss-0d2a72510.yml/badge.svg?branch=main)](https://github.com/MareMare/try-blazorwasm-standalone-singleOrg/actions/workflows/azure-static-web-apps-polite-moss-0d2a72510.yml) | https://polite-moss-0d2a72510.2.azurestaticapps.net |

## 'Cannot read properties of undefined (reading 'toLowerCase')'

GitHub Pages あるいは Azure Static site へ発行し、実際にサイトへアクセスすると次のエラーが表示される。
```
There was an error trying to log you in: 'Cannot read properties of undefined (reading 'toLowerCase')'
```

対処方法は *.csproj に以下を追加すれば良いらしい。
```xml
<ItemGroup>
  <TrimmerRootAssembly Include="Microsoft.Authentication.WebAssembly.Msal" />
</ItemGroup>
```

詳細は https://github.com/dotnet/aspnetcore/issues/38082#issuecomment-1072762015 。
