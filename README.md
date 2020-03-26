# 🥕 hello-world-github-packages

以下のドキュメントを見ながらやった備忘録である。

[Configuring npm for use with GitHub Packages - GitHub Help](https://help.github.com/en/packages/using-github-packages-with-your-projects-ecosystem/configuring-npm-for-use-with-github-packages)

作った Github Packages のリポジトリはこちら、

[Package hello-world-github-packages · hisasann/hello-world-github-packages](https://github.com/hisasann/hello-world-github-packages/packages/162978)

## package.jsonに以下を追加する

`npm publish` するときのレジストリーを指定します。

```json
"publishConfig": { "registry": "https://npm.pkg.github.com/" }
```

## GitHub Packagesはscoped npm packagesのみである

> GitHub Packages only supports scoped npm packages.

と書いてあるとおり、 `@OWNER/your-github-package-repository` のように **scoped** になる。

## Github Packagesにログインする

```bash
$ npm login --registry=https://npm.pkg.github.com/
```

* GitHub のユーザーネーム
* Personal access token
* mail address

を入力します。

Personal access token は、
GitHub の `Settings -> Developer settings -> Personal access tokens` で作成できます。

指定する権限は、以下。

* repo
* write:packages
* read:packages

## publishする

```bash
$ npm publish
```

[Your Packages](https://github.com/hisasann?tab=packages)

## ~/.npmrc

おそらくですが、 `npm login` すると以下の一行が `~/.npmrc` に追加される（ぼくの場合はファイル自体もなかったので、作成もされた）

```bash
//npm.pkg.github.com/:_authToken=TOKEN
```

## 公開したGithub Packagesをnpm installしてみる

以下のように普通に `npm install` すると、

```bash
$ npm install @hisasann/hello-world-github-packages@0.0.1
```

npmjs に取りにいってしまいエラーとなってしまうので、

```bash
npm ERR! 404 Not Found - GET https://registry.npmjs.org/@hisasann%2fhello-world-github-packages - Not found
```

## プロジェクト/.npmrcに以下を追記する

`npm install` するプロジェクトのルートで（package.jsonがあるディレクトリで）、 `.npmrc` ファイルに以下を追記します。

```bash
registry=https://npm.pkg.github.com/
```

これで、まずは Github Packages を見に行ってくれ、なければ npmjs のレジストリーに見に行ってくれます。

つまり、こちらは Github Packages から。

```bash
$ npm install @hisasann/hello-world-github-packages@0.0.1
```

こちらは、 npm 本家から。

```bash
$ npm install lodash
```

## Github Actions経由でnpm publishする

```yaml
name: Node.js Package
on:
  release:
    types: [created]
jobs:
  build:
    name: Github Packages Publish
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      # GitHub Packagesへの公開のために.npmrcファイルをセットアップ
      - uses: actions/setup-node@v1
        with:
          node-version: '12.x'
          registry-url: 'https://npm.pkg.github.com'
          scope: '@hisasann' # Defaults to the user or organization that owns the workflow file
      - run: npm install
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

via [Node.jsパッケージの公開 - GitHub ヘルプ](https://help.github.com/ja/actions/language-and-framework-guides/publishing-nodejs-packages)

## 参考記事

[GitHub Package Registry を npm で使う - Qiita](https://qiita.com/nall/items/5e94f37288c3e796a85e)

[GitHub Packages でパッケージを公開する](https://aggre.io/post/publish-package-with-github-packages)

## 🍟 Author

- [github/hisasann](https://github.com/hisasann)
- [twitter/hisasann](https://twitter.com/hisasann)

## 🥫 License

MIT © [hisasann (Yoshiyuki Hisamatsu)](https://github.com/hisasann)
