.. _windows_tools_vm:

----------------
Windows Tools VMの構築
----------------

はじめに
+++++++++

演習中試験クライアントとして用いるWindows 2012 R2のマシンを構築します。以下のツール群がインストールされています。

- Microsoft Remote Server Administration Tools (RSAT)
- PuTTY, CyberDuck, WinSCP
- Sublime Text 3, Visual Studio Code
- OpenOffice
- Python
- pgAdmin
- Chocolatey Package Manager

構築手順
++++++++++++++++++

#. **Prism Central** > select :fa:`bars` **> 仮想インフラ > 仮想マシン** において、 **仮想マシンを作成** をクリックします。

以下の情報を入力します。

- **Name** - *Initials*-Windows-ToolsVM
- **vCPU(s)** - 1
- **Number of Cores per vCPU** - 2
- **Memory** - 4 GiB

- Select **+ Add New Disk**
    - **タイプ** - DISK
    - **オペレーション** - イメージサービスからクローン
    - **イメージ** - WinToolsVM.qcow2
    - **Add** を選びます。

- Select **Add New NIC**
    - **Network Name** - Secondary
    - **追加** を選びます。

#. **Save** をクリックし仮想マシンを作成します。

#. 作成した仮想マシンを選択し、 **アクション > パワーオン** で電源を入れます。 

#. RDPもしくはコンソール接続にて作成した仮想マシンにログインします。

- **ユーザ名** - NTNXLAB\\Administrator
- **パスワード** - nutanix/4u
