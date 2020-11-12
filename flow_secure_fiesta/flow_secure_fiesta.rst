.. _pcflow_secure_fiesta:

-------------------------------
Flowを使用したアプリケーションの保護
-------------------------------

Flowは、Nutanix AHVおよびPrism Centralに統合されたアプリケーション中心のネットワークセキュリティ製品です。
AHV上で動作するVMに対して、豊富なネットワークトラフィックの可視化、自動化、およびセキュリティを提供します。

マイクロセグメンテーションはシンプルなポリシーベースの管理を使用してVMネットワーキングをセキュアにするFlowの機能です。
Prism Centralのカテゴリ（論理グループ）を使用して、強力な分散型ファイアウォールを作成することができます。また、Calmと組み合わせることで、作成したアプリケーションの安全性が確保された自動展開が可能になります。

**この演習では、Ntanix Calmを用いてFiestaというWeb/DBの2階層からなるWebアプリケーションへのアクセスを制限し、アプリケーション層間のトラフィックおよびクラスタ外からのトラフィックからアプリケーションを保護します。**

Fiestaアプリケーションの保護
+++++++++++++++++++++++

Flowは、仮想マシンを迅速にグループ化するために使用されるAppType、AppTier、Environmentなどの複数のシステムカテゴリを提供しています。
セキュリティポリシーは、これらのカテゴリを使用して適用されます。
これらの既存のカテゴリをそのまま使用することも、カスタムグループ化のために独自のカテゴリを追加することもできます。

カテゴリーの定義
........................

Prism Centralを使用して、カテゴリをメタデータとしてVMにタグ付けやポリシーがどのように適応されるべきか決定します。

#. **Prism Centralにログインし左上** の :fa:`bars` **> 仮想インフラ(Virtual Infrastructure) > カテゴリ(Categories)** と進みます。

#. **AppType** にチェックを入れ **(画面上部の)アクション(Actions)のプルダウンボタン** > **Update** をクリックします。

   .. figure:: images/12.png

#.  :fa:`plus-circle` アイコンをクリックして、値を入力します。

#. 値の名前として *あなたのイニシャル*-**Fiesta** を入力します。

   .. figure:: images/13.png

#. **保存(Save)** をクリックします。

#. 続いて同様に、 **AppTier** にチェックを入れ **(画面上部の)アクション(Actions)のプルダウンボタン** > **Update**  をクリックします。

#. :fa:`plus-circle` アイコンをクリックして、値を入力します。

#. 値の名前として *あなたのイニシャル*-**Web** を指定します。このカテゴリはWebアプリケーション層を想定して使用します。

#. 続けて :fa:`plus-circle` アイコンをクリックして、カテゴリに値を入力します。値の名前として *あなたのイニシャル*-**DB** を入力します。このカテゴリはMySQLデータベース層を想定して使用します。

   .. figure:: images/14.png

#. **保存(Save)** をクリックします。

セキュリティポリシーの作成
..........................

Nutanix Flowには、ネットワーク中心のアプローチではなく、ワークロード中心のアプローチを使用するポリシー駆動型のセキュリティフレームワークが含まれています。
そのため、VMのネットワーク構成がどのように変化しても、またデータセンターのどこに存在していても、VMへのトラフィックとVMからのトラフィックを精査することができます。また、ワークロード中心のネットワークに依存しないアプローチにより、仮想化チームはネットワークセキュリティチームに頼ることなく、これらのセキュリティポリシーを実装することができます。

セキュリティポリシーはカテゴリ毎に適用され、VM自体には適用されません。
したがって、与えられたカテゴリ内でどれだけの VM が存在していてもカテゴリに対して適用するため対象が増減しても同じ操作で適用する事ができます。
カテゴリ内の VM に関連付けられたトラフィックは、どの規模でも安全に保護されます。

Fiestaアプリケーションを保護するセキュリティポリシーを作成します。

#.  **Prism Centralにログインし左上** の :fa:`bars` **> ポリシー(Policies) > Security** をクリックします。

#. **セキュリティポリシーの作成(Create Security Policy) > セキュアアプリケーション(セキュリティポリシー) (Secure Applications (App Policy)) > 作成(Create)** をクリックします。

#. 次のフィールドに入力します。

   - **名前(Name)** - *あなたのイニシャル*-Fiesta
   - **目的(Purpose)** - Fiestaアプリケーションへのトラフィック保護
   - **このアプリを保護(Secure this app)** - AppType: *あなたのイニシャル*-Fiesta
   - **カテゴリ別にアプリタイプにフィルターをかけます** に **チェックを入れない** (Do **NOT** select **Filter the app type by category**).
   - **ポリシーヒットログ** - **有効**

   .. figure:: images/18.png

#. **次へ(Next)** をクリックします。

#. もしチュートリアルのプロンプトが表示されたら、**分かりました！(OK, Got it!)** をクリックします。

#. セキュリティポリシーをより詳細に設定するには、特定のAppTypeのすべてのコンポーネントに同じルールを適用するのではなく、 **代わりにアプリ階層にルールを設定します(Set rules on App Tiers, instead)** から行います。

   .. figure:: images/19.png

#. **+階層を追加(+ Add Tier)** をクリックします。

#. **AppTier:**\ *あなたのイニシャル*-**Web** をドロップダウンから追加します。

#. Steps 7-8 を同様に繰り返し、 **AppTier:**\ *あなたのイニシャル*-**DB** も追加します。

   .. figure:: images/20.png

   次に、アプリケーションとの通信を許可するソースを制御する **Inbound** ルールを定義します。
   すべての受信トラフィックを許可することも、ホワイトリストのソースを定義することもできます。

   **このシナリオでは、あなたのイニシャル-Windows-ToolsVMからTCPポート80 Web層へのインバウンドトラフィックのみ許可します。**

#. **インバウンド Traffic(Inbound Traffic)** の **移行元を追加(+ Add Source)** をクリックします。

#. 次のフィールドを入力し、全ての受信IPアドレスを許可の設定を行います。

   - **～で移行元追加:(Add source by:)** - で  **サブネット/IP(Subnet/IP)** を選択
   - **(あなたのイニシャル-Windows-ToolsVMのIPアドレス)/32**

　入力後、 **追加(add)** をクリックします。

   .. note::

     ソースはカテゴリで指定することもでき、この値はネットワークの場所変更に関係なくVMを追跡できるため、より柔軟性が高くなります

#. インバウンドルールを作成するために、 **AppTier:**\ *あなたのイニシャル*-**Web** 左側に表示される **+** アイコン をクリックします。

   .. figure:: images/21.png

    左側に **+** アイコンが表示されていない場合は、**インバウンドTraffic** で追加した、 **サブネット/IP 0.0.0.0/0** をクリックします。

#. 次のフィールドに入力します。

   - **Service Details** - Select a service
   - **Service Name** - http

   .. note::

     "+ Add Row"をクリックすることで1つのルールに複数のプロトコルとポートを設定できます。

#. **Save** をクリックします。

#. Calm は、スケールアウト、スケールイン、アップグレードなどのワークフローのために VM へのアクセスすることもあります。Calm は、TCP ポート 22 を使用して SSH 経由でこれらの VM と通信します。よってCalm(IPアドレスはPrism Centralのものを用います。)から各VMへのsshアクセスを許可する必要があります。

#. 続いて、 **インバウンド Traffic(Inbound Traffic)** 下の **+移行元を追加(+ Add Source)** をクリックします。

#. 次のフィールドに入力します。

   - **～で移行元追加:(Add source by:)** - で  **サブネット/IP(Subnet/IP)** を選択
   - *Prism CentralのIPアドレス*\ /32

   .. note::
     **/32** は範囲ではなく、単一のIPを示します。

   .. figure:: images/23.png

#. **追加(Add)** をクリックします。

#. **AppTier:**\ *あなたのイニシャル*-**Web** の左側に表示される **+** アイコンをクリックします。

#. 次のフィールドに入力します。

   - **Service Details** - Select a service
   - **Service Name** - ssh

#. **Save** をクリックします。

#. **AppTier:**\ *あなたのイニシャル*-**DB** に対しても同様の操作を繰り返し、Calmがsshを用いてデータベースVMとssh通信出来る様にします。

   .. figure:: images/24.png

   デフォルトでは、セキュリティポリシーにより、アプリケーションはすべての送信トラフィックを任意の宛先に送信できます。この場合、アプリケーションに必要な唯一のアウトバウンド通信は、DNSサーバーとの通信です。

#. **アウトバウンド Traffic(Outbound Traffic)** 下の **ホワイトリストのみ(Whitelist Only)** を選択し、 ドロップダウンメニューから **+デスティネーションを追加(+ Add Destination)** を選択します。

#. 次のフィールドに入力します。

   - **ディスティネーションの追加(Add destination by:)** をクリックし、 **～で移行元追加:(Add source by:)** - で **サブネット/IP(Subnet/IP)** を選択
   - *ドメインコントローラのIP*\ /32
   
   .. figure:: images/25.png

#. **追加(Add)** をクリックします。

#. **AppTier:**\ *あなたのイニシャル*-**Web** の右側に表示される **+** アイコン を選択します。

#. 次のフィールドに入力します。

   - **Service Details** - Select a service
   - **Service Name** - domain

#. **Save** をクリックします。

#. 同様に **AppTier:**\ *あなたのイニシャル*-**DB** に対しても行います。

   .. figure:: images/26.png

   アプリケーション層はDB層と通信を必要としポリシーはこのトラフィックを許可する必要があります。またWeb層は、同じ層内での通信を必要としません。

#. アプリケーション内の通信を定義するには、**アプリ内でルールを設定(Set Rules within App)** をクリックします。

   .. figure:: images/27.png

#. **AppTier:**\ *あなたのイニシャル*-**Web** を選択し **No** をクリックして、この層内のVM間の通信を禁止します。層内に存在するWeb VMは1つのみです。

#. 続いて、**AppTier:**\ *あなたのイニシャル*-**Web** を選択した状態で **AppTier:**\ *Initials*-**DB** の :fa:`plus-circle` アイコンをクリックして、層間のルールを作成します。

#. 次のフィールドに入力します。

   - **Service Details** - Select a service
   - **Service Name** - mysql

#. **Save** をクリックします。

#. 画面右上の **次へ(Next)** をクリックして、ここまで設定してきたセキュリティポリシーを確認します。

#. **保存とモニター(Save and Monitor)** をクリックしてポリシーを保存します。


ブループリントの構築
..........................

上で作成したFlowによるセキュリティポリシーを実際に動作させてみます。WebサーバとDBサーバの複数VMによるブループリントを構築し、ブループリント内でアプリケーションに対してカテゴリを付与します。最終的にこのブループリントをIT利用者に対して公開することで、セキュリティポリシーの自動・強制適用が可能となり、仮想基盤、データセンターネットワークに渡る運用プロセスの自動化が可能となります。

#. **Prism Central** で、 :fa:`bars` **> サービス > Calm** を選択します。

   .. figure:: images/1_access_calm.png

#. 左側のツールバーの **Blueprints** を選択して、Calmのブループリントを表示および管理します。

   .. note::

     アイコンにマウスを当てるとメニューがテキストで表示されます。

#. `こちら <https://raw.githubusercontent.com/nutanixworkshops/EnterprisePrivateCloud_Bootcamp-Japanese/master/dayinlife/Fiesta-Multi.json>`_ からテンプレートとなるブループリントをローカルマシンにダウンロードします。(ブラウザの機能においてファイルを別名ダウンロードしてください。)

#. **ブループリントのアップロード** をクリックし、ダウンロードしたjsonファイル(Fiesta-Multi.json)を選択します。

#. 以下の項目を記入します。

   - **ブループリント名** - *あなたのイニシャル*-Fiesta
   - **プロジェクト** - *あなたのイニシャル*-Project

#. 以下の項目を記入します。

   - **説明** - ブループリントの説明を書きます。(任意)
   - **プロジェクト** - *あなたのイニシャル*-Project(変更なし)

#. ブループリントを起動するには、最初にネットワークをVMに割り当てる必要があります。 **NodeReact_AHV** のアイコンをクリックし、画面右側の **仮想マシン** タブから次の項目を設定します。

   - **NIC 1** - **Primary**
   - **プライベートIP** - **動的**
   - **カテゴリ**
   - AppType: *あなたのイニシャル*-Fiesta
   - AppTier: *あなたのイニシャル*-Web

#. 次に **MySQLAHV** のアイコンをクリックし、画面右側の **仮想マシン** タブから次の項目を設定します。

#. 同じく **仮想マシン** タブから **カテゴリ** メニューにおいて以下を選択します。

   - **NIC 1** - **Primary**
   - **プライベートIP** - **動的**
   - **カテゴリ**
   - AppType: *あなたのイニシャル*-Fiesta
   - AppTier: *あなたのイニシャル*-DB

#. **認証情報** をクリックし、ブループリントによってプロビジョニングされるCentOS VMへの認証に使用される秘密鍵を定義します。

   .. figure:: images/27b.png

#. **CENTOS** の認証情報で"Edit"をクリックし、以下の値を **SSH秘密キー** に入力します。

   ::

      -----BEGIN RSA PRIVATE KEY-----
      MIIEowIBAAKCAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNG
      ZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK
      6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9
      HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7Gy
      hCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNR
      uz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5OrwIBJQKCAQB++q2WCkCmbtByyrAp
      6ktiukjTL6MGGGhjX/PgYA5IvINX1SvtU0NZnb7FAntiSz7GFrODQyFPQ0jL3bq0
      MrwzRDA6x+cPzMb/7RvBEIGdadfFjbAVaMqfAsul5SpBokKFLxU6lDb2CMdhS67c
      1K2Hv0qKLpHL0vAdEZQ2nFAMWETvVMzl0o1dQmyGzA0GTY8VYdCRsUbwNgvFMvBj
      8T/svzjpASDifa7IXlGaLrXfCH584zt7y+qjJ05O1G0NFslQ9n2wi7F93N8rHxgl
      JDE4OhfyaDyLL1UdBlBpjYPSUbX7D5NExLggWEVFEwx4JRaK6+aDdFDKbSBIidHf
      h45NAoGBANjANRKLBtcxmW4foK5ILTuFkOaowqj+2AIgT1ezCVpErHDFg0bkuvDk
      QVdsAJRX5//luSO30dI0OWWGjgmIUXD7iej0sjAPJjRAv8ai+MYyaLfkdqv1Oj5c
      oDC3KjmSdXTuWSYNvarsW+Uf2v7zlZlWesTnpV6gkZH3tX86iuiZAoGBAKM0mKX0
      EjFkJH65Ym7gIED2CUyuFqq4WsCUD2RakpYZyIBKZGr8MRni3I4z6Hqm+rxVW6Dj
      uFGQe5GhgPvO23UG1Y6nm0VkYgZq81TraZc/oMzignSC95w7OsLaLn6qp32Fje1M
      Ez2Yn0T3dDcu1twY8OoDuvWx5LFMJ3NoRJaHAoGBAJ4rZP+xj17DVElxBo0EPK7k
      7TKygDYhwDjnJSRSN0HfFg0agmQqXucjGuzEbyAkeN1Um9vLU+xrTHqEyIN/Jqxk
      hztKxzfTtBhK7M84p7M5iq+0jfMau8ykdOVHZAB/odHeXLrnbrr/gVQsAKw1NdDC
      kPCNXP/c9JrzB+c4juEVAoGBAJGPxmp/vTL4c5OebIxnCAKWP6VBUnyWliFhdYME
      rECvNkjoZ2ZWjKhijVw8Il+OAjlFNgwJXzP9Z0qJIAMuHa2QeUfhmFKlo4ku9LOF
      2rdUbNJpKD5m+IRsLX1az4W6zLwPVRHp56WjzFJEfGiRjzMBfOxkMSBSjbLjDm3Z
      iUf7AoGBALjvtjapDwlEa5/CFvzOVGFq4L/OJTBEBGx/SA4HUc3TFTtlY2hvTDPZ
      dQr/JBzLBUjCOBVuUuH3uW7hGhW+DnlzrfbfJATaRR8Ht6VU651T+Gbrr8EqNpCP
      gmznERCNf9Kaxl/hlyV5dZBe/2LIK+/jLGNu9EJLoraaCBFshJKF
      -----END RSA PRIVATE KEY-----

#. **保存** をクリックし、終了後 **戻る** をクリックします。

#. 秘密変数の値が空であることを示す **警告** が表示されます。これは、ブループリントを保存する際に秘密変数に値が入れられないため、この警告が発生します。しかし、この変数はユーザがブループリントからアプリケーションを起動する際に入力させる変数のため無視可能です。警告によって、ユーザーがブループリントを起動したり公開したりすることができなくなることはありません。その他の警告や赤いエラーが表示された場合は、先に進む前に問題を解決してください。

ブループリントの起動
..........................

#. **起動** ボタンをクリックして、以下のように入力してください。

    - **アプリケーションの名前** - *あなたのイニシャル*-Fiesta
    - **db_password** - Nutanix/4u

#. **作成** をクリックすると、アプリケーションのページが表示されます。

#. アプリケーションが **プロビジョニング** 状態から **実行中** 状態に変わるまで数分待ちます。 **エラー** 状態に変わった場合は、 **監査** タブに移動し、 **作成** アクションを展開して、問題のトラブルシューティングを開始します。

#. 起動が完了したら **サービス** タブに移動し、MySQLとNodeReactのアイコンをクリックすると、画面右側に仮想マシンのIPアドレスが表示されます。これをメモします。

セキュリティポリシーの監視と適用
+++++++++++++++++++++++++++++++++++++++++

ポリシーを適用する前に、Fiestaアプリケーションが期待通りに機能していることを確認します。

アプリケーションのテスト
.......................

#. *あなたのイニシャル*\ **-WinToolsVM** を起動します。

#. *あなたのイニシャル*\ **-WinToolsVM** のコンソールからブラウザを開き、 \http://*NodeReact VMのIPアドレス*/ にアクセスします。FiestaのWebアプリケーションが読み込めることを確認します。

#. コマンドプロンプトを開き、``ping -t NodeReact VMのIPアドレス`` と実行し、クライアントとWebサーバーVM間で通信が出来ていることを確認します。 **pingコマンドを中断せず実行したままとします。**

   .. figure:: images/31.png

Flowによる可視化
........................

#. **Prism Central** の :fa:`bars` **> ポリシー > Security** と進み、 *あなたのイニシャル*-**Fiesta** をクリックします。

#. **あなたのイニシャル-Windows-ToolsVMのIPアドレス** がインバウンドソースとして表示されていることを確認します。ソースとラインは黄色で表示され、クライアントVMからのトラフィックが検出されたことを示します。

   .. figure:: images/34_1.png

   他に検出されたトラフィックフローがあった場合は、それらの線にマウスカーソルを合わせて使用中のポート情報を確認してみます。
   もし、表示されない場合は、スキップして次の項へ進みます。

#. ここでは **あなたのイニシャル-Windows-ToolsVMのIPアドレス** からのICMPトラフィックをブロック対象とみなし、フィルタリングを行います。

#. 定義したポリシーを適用するために、 **適用(Apply)** ボタンをクリックします。

#. 確認ダイアログに **APPLY** と入力し **OK** をクリックすることでポリシーが適用されます。

#. **あなたのイニシャル-Windows-ToolsVM** コンソールに戻ります。

#. NodeReact VMへのpingトラフィックがブロックされた事を確認します。

     .. figure:: images/37_1.png

#. クライアントVMがWebブラウザとWebサーバーのIPアドレスを使用して、Fiestaアプリケーションに引き続きアクセスできることを確認します。

まとめ
+++++++++

- マイクロセグメンテーションは、データセンター内から発生し、1台のマシンから別のマシンへと横方向に拡散する悪意のある脅威に対する追加の保護を提供します
- Prism Centralで作成されたカテゴリは、Calmのブループリント内で利用できます。
- セキュリティポリシーは、Prism Centralのテキストベースのカテゴリを活用します。
- Nutanix Flowは、AHV上で動作するVMの特定のポートやプロトコルのトラフィックを制限することができます。
- ポリシーはMonitorモードで作成され、ポリシーが適用されるまでトラフィックはブロックされません。これは、接続を学習し、意図せずにトラフィックがブロックされないようにするのに役立ちます。
