.. _lab_deploy_workloads:

-------------------------
ワークロードの導入
-------------------------

概要
++++++++

ストレージに加えて、VMの作成、管理、モニタリングはNutanix AHVに対してすべてPrismを介して行うことができます。

.. note::

  また、PrismではvCenterを登録することで、ESXiを実行しているNutanixクラスタのVM CRUD操作をネイティブにサポートしています

ワークロードの導入
+++++++++++++++++++

次の演習では、ソースメディアからのVMの作成と既存のディスクイメージからのVMの作成について説明します。

Windows VMの作成
.....................

この演習では、Windows インストール ISO イメージから Windows Server VM を作成します。

AHV は **Image Service** を提供しており、VM作成時、ISOイメージからCD-ROMの作成や、ディスクイメージからOSシステムディスクの作成に使用するインポートファイルを格納できます。Image serviceは、raw、vhd、vhdx、vmdk、vdi、iso、およびqcow2のディスクフォーマットをサポートしています。

.. note::

   Prism Element の :fa:`cog` **> Image Configuration** で利用可能なイメージを探したり、追加のイメージをアップロードしたりできます。

VM にハイパフォーマンスI/Oを提供するためにはAHV はゲストに準仮想化ドライバをインストールする必要があります（VMware Tools に似ています）。特に Windows ゲストの場合、Windows インストーラで VM のディスクにアクセスできるようにするためにはインストール中にこれらのドライバを読み込む必要があります。

Nutanixはこれらのドライバを検証し、http://portal.nutanix.com を通して配布しています。また、本環境ではドライバを含むISOイメージもImage Serviceにアップロードされています。

#. **Prism Element > VM > Table** と進み **+ Create VM** をクリックします。

#. 次のフィールドに入力し、 **Save** をクリックします。

    他の設定はデフォルト値のままにします。

   - **Name** - *Initials*-Windows_VM
   - **Description** - (Optional) Description for your VM.
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB
   - CDROMの隣の :fa:`pencil` を選択してください
       - **Operation** - Clone from Image Service
       - **Image** - Windows2016.ISO
       - **Update** を選択してください

       *これは、ブート/インストール用のImage ServiceからWindows Server ISOをマウントします。*

   - **+ Add New Disk** をクリックします。
       - **Type** - DISK
       - **Operation** - Allocate on Storage Container
       - **Storage Container** - Default Container
       - **Size (GiB)** - 30 GiB
       - **Add** を選択してください

       *選択したStorage Containerに30GiBのvDiskを作成します*

   - **+ Add New Disk** をクリックします。
       - **Type** - CDROM
       - **Operation** - Clone from Image Service
       - **Image** - Nutanix VirtIO ISO
       - **Add** を選択してください
       - **Boot Configuration**
       - デフォルトで選択された **Legacy Boot** はそのままにしてください

   - **Add New NIC** をクリックします。
       - **VLAN Name** - Primary
       - **Add** を選択してください

       *選択した仮想ネットワーク上のVMに1つの仮想NICを追加します*

#. **Save** をクリックし、仮想マシンを作成します。

       .. note::

         以下のURLにサポートOSが掲載されています。

         http://my.nutanix.com/uefi_boot_support

#. VM を選択し、テーブルの下にある **Power On** をクリックして VM をオンにします。

   .. figure:: images/vm_power_on.png

#. VM を選択し、**Actions** ドロップダウンメニューから **Launch Console** をクリックして、VMを操作するためのHTML5 コンソールにアクセスします。

#. Windowsのインストール場所の画面にいくまで、標準のインストールの質問を進めてください。

   .. note::
     選択肢が表示されたら、**Datacenter with GUI** と **Custom** インストールを選択してください。

#. **Load Driver** をクリックし、Nutanix VirtIO がマウントされているドライブに移動します。

#. CDをブラウズし、インストールするWindows OSに対応するディレクトリを選択します。

   .. figure:: images/deploy_workloads_05.png

   .. figure:: images/deploy_workloads_06.png

#. 表示された3つのNutanixドライバを選択します（Ctrlキーを押しながら3つのドライバをすべてクリックします）

   - Balloon
   - Ethernet adapter
   - SCSI passthrough controller

   .. figure:: images/deploy_workloads_07.png

#. **Next** をクリックします。

   ドライバが読み込まれると、手順2で作成したディスクがインストール対象として表示されます。

#. そのディスクを選択して、通常のインストールプロセスを行います。

#. インストールが完了した後、必要に応じて、Windows installとVirtIOのISOをWindows内からアンマウントできます。
   テーブル内のVMを選択してVMからCD-ROMを削除できます。具体的には、アクションリンクのリストから **Update** をクリックし、CD-ROMディスクを削除します(VMは電源OFFが必須です)。

   .. note::

     ESXi クラスタの場合、VMware vSphere で VM が作成されると、VMがPrism VMs リストに表示されます。Prism で VM が作成された場合は、VMware vSphere UI に表示されます。手動での同期や待機は必要ありません。

     .. figure:: images/deploy_workloads_08.png

#. OSのインストール後、PrismでVMを選択し、**Manage Guest Tools > Enable Nutanix Guest Tools > Mount Guest Tools** の順にクリックし、**Submit** をクリックすることで、**Nutanix Guest Tools (NGT)** のインストール準備をします。

   これは仮想 CD-ROM ドライブを使用して NGT インストール ISO を VM にマウントします。NGTには、過去にインストールされたVirtIOドライバのほか、 **Self-Service File Restore (SSR)** および **Application Consistent (VSS) snapshots** をサポートするサービスが含まれています

    .. figure:: images/deploy_workloads_nutanix_guest_tools.png

#. VMコンソールに戻り、Nutanix Guest Tools の CDドライブをクリックしてNGTのインストールを完了させます。

    .. figure:: images/deploy_workloads_ngt_mounted.png

Linux VMの作成
...................

このエクササイズではImage ServiceにプリインストールされているディスクイメージからCentOS VMを作成します。 一般的にはテンプレートスタイルのディスクイメージがプリインストールされています。 また、この演習で使うインストールイメージもImage Serviceにあります。

#. **Prism Element > VM > Table** と進み、**+ Create VM** をクリックします。

#. 以下のフィールドを入力し、 **Save** をクリックします。

   - **Name** - *Initials*-Linux_VM
   - **Description** - (Optional) Description for your VM.
   - **vCPU(s)** - 1
   - **Number of Cores per vCPU** - 1
   - **Memory** - 2 GiB

   - **+ Add New Disk** をクリックします。
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - CentOS7.qcow2
      - **Add** を選択してください。
      - **Boot Configuration**
      - デフォルトで選択された **Legacy Boot** はそのままにしてください。

    *既存のCentOSディスクイメージのシン・クローンを作成します。*

   - **Add New NIC** をクリックします。
      - **VLAN Name** - Primary
      - **Add** を選択してください。

   .. figure:: images/deploy_workloads_03.png

#. **Save** をクリックしVMを作成します。

#. コンソールを起動して、VMが起動しているのを確認します

まとめ
+++++++++

- このラボでは、Windows VM と Linux VM の構築がいかに簡単かを見てきました。
- Image Configurationツールを利用するのは、VMの構築で必要なインストールイメージのカタログとして使用します。qcow、qcow2、vmdk、VHD、VHDx、RAW、ISO を含む様々なフォーマットをサポートします。
