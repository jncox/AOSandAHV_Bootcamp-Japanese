.. _lab_manage_workloads:

------------------------
ワークロード管理
------------------------

概要
++++++++

いくつかの VM がデプロイされたので、AHV を使った VM 管理タスク (電源管理、検索、クローン、マイグレーション) を操作してみましょう。

ワークロードの管理
+++++++++++++++++++

電源操作とコンソールアクセス
................................

VMの電源操作とコンソールアクセスについて説明します。

#. **Prism Element > VM > Table** と進み、前のエクササイズ作成した仮想マシン *Initials*- **Linux_VM** を見つけます。

   .. note::

   その VM の Power State 列には赤いドットが表示され、VM が電源オフであることを示していることに注意してください。

#. その仮想マシンを選択して **Power On** をクリックします。

#. その仮想マシンを選択して **Launch Console** をクリックします。

   コンソールウィンドウには 4つのアクションが用意されています。 **Mount ISO, CTRL-ALT-DEL, Screen Capture, 電源操作** です。

   .. figure:: images/manage_workloads_01_a.png

   .. note::

     In ESX:

     - このエクササイズの手順は、VMware vCenterインスタンスがPrismに登録されているESXiクラスタを使用しているときに、Prismから実行できます。

     .. figure:: images/manage_workloads_06.png

仮想マシンのクローン
...........

#. **Prism Element > VM > Table** と進み、 *Initials*-**Linux_VM** を選択します。

#. **Actions** リストから **Clone** をクリックします。

#. 次のフィールドに入力し、 **Save** をクリックします。


   - **Number of Clones** - 2
   - **Prefix Name**  - *Initials*-Linux-Clone
   - **Starting Index Number** - 1

   他の設定はデフォルト値のままにします。

   .. figure:: images/manage_workloads_02.png

#. 電源操作は行わず **Powered Off** のままにします。

  Nutanix におけるスナップショットとクローンは いずれも  `redirect-on-write <https://nutanixbible.com/#anchor-book-of-acropolis-snapshots-and-clones>`_ アルゴリズムを使用して、メタデータを操作することで迅速かつ効率的に作成します。

ホスト間における仮想マシンの移行
............................

仮想マシンのライブマイグレーションは、あらゆる仮想化環境にとって重要な機能であり、VMをクラスタ内のホスト間でシームレスに移動させ、インフラストラクチャのメンテナンスやパフォーマンスバランシングを可能にします。

#. **Prism Element > VM > Table** と進み、 *Initials*-**Linux_VM** を選択します。

   電源オフ状態では、 **Host** 欄に何も値がないことを確認します。

   .. figure:: images/manage_workloads_03.png

#. 仮想マシンを選択し、**Powered On** をクリックします。その後 **Migrate** をクリックします。

   仮想マシンの移行対象としてクラスタ内の他のホストのいずれかを選択するか、デフォルトを受け入れて AHV が自動的に場所を選択するようにすることができます。

#. **Migrate** をクリックして、アクションを実行します。

   タスクが完了したら、VMホストの場所がマイグレーション前のホストから選択した新しいホストに変更されたことを確認します

   .. figure:: images/manage_workloads_04.png

.. note::
   マイグレーションの進捗状況を確認するには、画面下部の「All VM Tasks」タブをクリックします。以下は、マイグレーションのスクリーンショットです。

 .. figure:: images/manage_workloads_03_a.png

アフィニティポリシーの構成
.............................

#. **Prism Element > VM > Table** と進み *Initials*-**Linux_VM** を選択します。

#. **Powered Off** を押し、電源状態をオフにして、**Update** そして **+ Set Affinity** とクリックします。

#. アフィニティ設定をするために、2つの **Hosts** を選択し、 **Save** 、 **Save** とクリックします。

   .. note:: 複数のホストを選択して、ノード障害が発生した場合にVMにも移行する場所を用意します。

#. 仮想マシンの電源を入れ アフィニティポリシーで選択した **Hosts** 上で起動したことを確認します。

#. 仮想マシンを選択し **Migrate** をクリックします。

   次のメッセージが表示されます

   - This VM has host affinity with 2 out of the 4 available hosts. It can only be migrated to those hosts.

#. **Migrate** をクリックします

   仮想マシンが他のホストに移動したことが分かります。

VM-to-Hostアフィニティルールは、一般的にパフォーマンスやライセンスの理由から、VMを特定のホストにマッピングするために使用されます。AHVでは、VM対VMのアンチアフィニティルールを作成することもできます。これは、アプリケーションの複数のインスタンスが同じノード上で実行されないようにする必要がある高可用性アプリケーションによく使用されます。

高可用性と動的スケジューリング
......................................

ESXiとは異なり、AHVではデフォルトでハイアベイラビリティが有効になっており、ホスト障害が発生した場合にベストエフォートな方法で仮想マシンを再起動します。追加設定でリソースの予約を設定して、HA イベント時に容量を確保することができます。

.. note::

  メモリ予約を有効にするには、: fa:`cog` **> Manage VM High Availability** で **Enable HA Reservation** を選択します
  共有クラスタリソースのメモリはすでに制限されているため、HAメモリ予約を有効にしないでください。

AHVは、**Acropolis Dynamic Scheduler** サービスを使用してVMのインテリジェントな初期配置を実行し、ワークロードのパフォーマンスを最適化するためにVMをクラスタ内の他のホストに動的に移行することができます。これは、追加の設定なしに実行可能です。

  Nutanix AHVソリューションの利点は、CPU/メモリの輻輳回避だけでなく、ストレージ性能に基づいてVMの配置を決定できることです。

**Acropolis Dynamic Scheduler** について追加情報は `here <https://nutanixbible.com/#anchor-book-of-acropolis-dynamic-scheduler>`_ を参照してください。

Prism Search
............

Prismの検索機能を使用すると、Prism Centralで問題を特定したり、機能ドキュメントを見つけたりするのが簡単になります。いくつかの検索クエリを入力してPrism Centralの検索機能を使用して、上記のタスクをどれだけ簡単にできるかを確認してください


例:

- vm cpu > 1
- vm mem > 2
- vm iops
- create vm
- powered on
- powered on cpu = 8

#. **Prism Central > ** :fa:`search` を使って検索します

- Note the result types: Entity, Alerts, and Help.
- Click the star icon to save a search.

.. note::

  検索ホットキー（スラッシュマーク、または/）は、Prism Central UIのどこからでも検索機能を呼び出せます。

まとめ
+++++++++

- このラボでは、クラスタ内のVMを管理するためのツールとアクションのコンプリートセットをAHVがどのように提供しているかを体験しました。
- ESXiクラスタにおいてはvCenterをPrismに登録することで、基本的なVM管理タスクの一部をPrismから実行できるようにできます。
