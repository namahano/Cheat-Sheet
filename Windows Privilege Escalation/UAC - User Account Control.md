# User Account Control

[User Account Control (UAC)](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works) は、昇格されたアクティビティに対する同意プロンプトを有効にする機能。アプリケーションにはさまざまな`integrity`レベルがあり、高いレベルのプログラムはシステムを侵害する可能性のあるタスクを実行する可能性がある。 UAC が有効になっている場合、管理者がこれらのアプリケーション/タスクにシステムへの管理者レベルのアクセス権を付与して実行することを明示的に許可しない限り、アプリケーションとタスクは常に管理者以外のアカウントのセキュリティ コンテキストで実行される。これは管理者を意図しない変更から保護する便利な機能ですが、セキュリティ境界とはみなさない

UACが適用されている場合、管理者ユーザーには2つのトークンが与えられる：通常のアクションを通常のレベルで実行するための標準ユーザーキーと、管理者特権を持つもの

## UAC に対して設定できるグループ ポリシー設定

| Group Policy Setting                                         | Registry Key                | Default Setting                                              |
| ------------------------------------------------------------ | --------------------------- | ------------------------------------------------------------ |
| [User Account Control: 組み込みの管理者アカウントの管理者承認モード](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-admin-approval-mode-for-the-built-in-administrator-account) | FilterAdministratorToken    | Disabled                                                     |
| [User Account Control: UIAccess アプリケーションがセキュリティで保護されたデスクトップを使用せずに昇格を要求することを許可する](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-allow-uiaccess-applications-to-prompt-for-elevation-without-using-the-secure-desktop) | EnableUIADesktopToggle      | Disabled                                                     |
| [User Account Control: 管理者承認モードでの管理者の昇格プロンプトの動作](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-behavior-of-the-elevation-prompt-for-administrators-in-admin-approval-mode) | ConsentPromptBehaviorAdmin  | Prompt for consent for non-Windows binaries                  |
| [User Account Control: 標準ユーザーに対する昇格プロンプトの動作](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-behavior-of-the-elevation-prompt-for-standard-users) | ConsentPromptBehaviorUser   | Prompt for credentials on the secure desktop                 |
| [User Account Control: アプリケーションのインストールを検出し、昇格を求める](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-detect-application-installations-and-prompt-for-elevation) | EnableInstallerDetection    | Enabled (default for home) Disabled (default for enterprise) |
| [User Account Control: 署名され検証された実行ファイルのみを昇格する](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-only-elevate-executables-that-are-signed-and-validated) | ValidateAdminCodeSignatures | Disabled                                                     |
| [User Account Control: 安全な場所にインストールされている UIAccess アプリケーションのみを昇格する](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-only-elevate-uiaccess-applications-that-are-installed-in-secure-locations) | EnableSecureUIAPaths        | Enabled                                                      |
| [User Account Control: すべての管理者を管理者承認モードで実行する](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-run-all-administrators-in-admin-approval-mode) | EnableLUA                   | Enabled                                                      |
| [User Account Control: 昇格を求めるときにセキュリティで保護されたデスクトップに切り替える](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-switch-to-the-secure-desktop-when-prompting-for-elevation) | PromptOnSecureDesktop       | Enabled                                                      |
| [User Account Control: ファイルとレジストリの書き込みエラーをユーザーごとの場所に仮想化する](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/user-account-control-group-policy-and-registry-key-settings#user-account-control-virtualize-file-and-registry-write-failures-to-per-user-locations) | EnableVirtualization        | Enabled                                                      |

## UACが有効になっていること確認する

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

もし`0x1`であればUACは有効になっている

## UACレベルの確認

```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

- `0x0` : UAC はプロンプトを表示しない(無効になっているなど)
- `0x1` : 管理者は、(Secure Desktop上で) 高い権限でバイナリを実行するためにユーザー名とパスワードの入力を求められる
- `0x2` : UAC は、(Secure Desktop 上で) 高い権限で何かを実行しようとすると、管理者に常に確認を求める
- `0x3` : `0x1` と同様ですが、Secure Desktop では必要ない
- `0x4` : `0x2` と同様ですが、Secure Desktop では必要ない
- `0x5` : Windows 以外のバイナリを高い特権で実行するかどうかを管理者に確認するように求められる(デフォルト)

## Windowsのバージョンを確認する

```
PS C:\> [environment]::OSVersion.Version

Major  Minor  Build  Revision
-----  -----  -----  --------
10     0      14393  0
```

この[ページ](https://en.wikipedia.org/wiki/Windows_10_version_history)でビルドバージョンからWindowsのリリース情報を確認できる

## UACバイパスエクスプロイト

[UACME](https://github.com/hfiref0x/UACME) プロジェクトは、影響を受ける Windows ビルド番号、使用された技術、および Microsoft がそれを修正するセキュリティ アップデートを発行したかどうかに関する情報を含む、UAC バイパスのリストを管理している（使用する際にはVisual Studioでビルドする必要がある）

`UACME`を見ると、ほとんどのUACバイパスが**Dllハイジャック脆弱性**を悪用している(悪意のあるdllを_C:\Windows\System32_に書き込むことが主な方法)

[DLLハイジャック脆弱性を見つける](https://book.hacktricks.xyz/v/jp/windows-hardening/windows-local-privilege-escalation/dll-hijacking)



