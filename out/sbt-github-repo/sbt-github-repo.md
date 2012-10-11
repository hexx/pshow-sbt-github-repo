!SLIDE
# sbt-github-repo

<br/>

[@pab_tech](https://twitter.com/pab_tech "@pab_tech")

!SLIDE
## 概要

<br/>

- GithubをMavenリポジトリとして使えるようにするsbtプラグイン
- https://github.com/hexx/sbt-github-repo

!SLIDE
## いいところ

<br/>

- 設定が簡単！ 新しいリポジトリを作って`build.sbt`に二行足すだけ
- gh-pagesを使っているので、リポジトリのURLが短くて済む

!SLIDE
## Publishする手順

<br/>

### https://github.com/hexx/repo というレポジトリを作りたい

<br/>

- Githubで新しいリポジトリを作成する
- リポジトリに入れたいライブラリにsbt-github-repoをインストール
- `build.sbt`で以下の二つを設定

<br/>

```scala
localRepo := Path.userHome / "github" / "repo"

githubRepo := "git@github.com:hexx/repo.git"
```

<br/>

- `sbt publish-to-github-repo`で登録完了

!SLIDE
## 作成したレポジトリに入っているライブラリを使う場合

<br/>

### https://github.com/hexx/repo というレポジトリを使いたい

<br/>

以下の設定を足す

<br/>

```scala
resolvers += "Hexx releases" at "http://hexx.github.com/repo/releases"
```

!SLIDE
## 何やってるの？

<br/>

- Gitの操作してるだけです
- sbt-gitを使っています
- 50行程度のソースで見ればすぐにわかります

<br/>

```scala
def createRepoTask(r: GitRunner, l: File, g: String, s: TaskStreams) {
  def git(args: String*) = r(args:_*)(l, s.log)
  git("init")
  l / "index.html" createNewFile()
  git("add", ".")
  git("commit", "-a", "-m", "first commit")
  git("checkout", "--orphan", "gh-pages")
  git("rm", "-rf", ".")
  IO.write(l / "index.html", "Maven Repository created by sbt-github-repo")
  git("add", ".")
  git("commit", "-a", "-m", "first commit")
  git("remote", "add", "origin", g)
  git("push", "origin", "gh-pages")
}
```

!SLIDE
## ちゃんとMaven Centralリポジトリに入れたいという人には…

<br/>

Sonatype経由で登録する方法があります。

<br/>

[自分が作ったScalaライブラリをMaven Centralリポジトリに登録する方法](http://pab-tech.tumblr.com/post/23240310972/scala-maven-central "自分が作ったScalaライブラリをMaven Centralリポジトリに登録する方法")

<br/>

前に僕の書いた解説です。よかったら参考にしてください。
