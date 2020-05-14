.. _lab_storage_configuration:

---------------------------
ストレージ構成
---------------------------

概要
++++++++

このラボでは、Prismを使用して基本的なコンテナ設定を実行します。

分散ストレージファブリック
++++++++++++++++++++++++++

NutanixのDistributed Srorage Fabric(DSF)はハイパーバイザからは集中管理型のストレージアレイに見えますが、各ノードの CVM とローカルストレージを使用してクラスタへ共有ストレージを提供します。
コンピュートと分散ストレージの組み合わせは、現在一般的に **ハイパーコンバージドインフラストラクチャ (HCI)** と呼ばれるものです。

.. figure:: images/dsf_overview.png

HCI分野でのパイオニアとして、DSFはエンタープライズデータベース、仮想デスクトップ、ROBO、ビックデータなどの様々なワークロードをサポートするために必要なパフォーマンスと弾性を提供できる成熟したソリューションです。

DSF内の2つの主なストレージ構造は、**Storage Pool** と **Storage Containers** です。

**Storage Pool** は、与えられたNutanixクラスタ内のすべての物理ディスクの集合体です。
クラスタはデータの分配を管理するため、追加のストレージプール(従来のストレージ環境のLUNのようなもの)の設定は必要ありません。
新しいノードがクラスタに追加されると、ディスクは自動的にプールに追加され、クラスタはバックグラウンドタスクとして新しいディスクにデータの再配置を開始します。
`バイブル <https://nutanixbible.com/#anchor-book-of-acropolis-disk-balancing>`_
`here <https://nutanixbible.com/#anchor-book-of-ahv-networking>`_


**Storage Containers** は、VMまたはvDisksのグループに対してストレージポリシーを構成することができるソフトウェアで定義された論理的な構造になっています。 次のエクササイズでは、Prism内でNutanixストレージを作成して構成するプロセスを説明します。


.. note::

   vDisks、エクステント、エクステントグループなどの追加のDSF構成については、Nutanixバイブルの以下のセクションを参照してください。
   `<https://nutanixbible.com/#anchor-book-of-acropolis-distributed-storage-fabric>`

このラボでは、Prism Elementsを使用して基本的なストレージコンテナのセットアップを行います

Prism Elementストレージ構成アイテム
+++++++++++++++++++++++++++++++++++++++++

ストレージコンテナの構成
............................

Prism Elementを使用して、基本的なコンテナー設定を実行してみましょう。

#. **Prism Element> Storage** と進み、 **Storage** の **Table** そして **+ Storage Container** をクリックします。

#. 次の値を入力してください。

   - **Name** - *Initials*-container
   - Select **Advanced Settings**
   - **Advertised Capacity** - 500 GiB
   - Select **Compression**
   - **Delay (In Minutes)** - 0

   .. note::

     他の設定はデフォルト値のままにします。

#. **Save** をクリックします。

   .. figure:: images/storage_config_01.png

   これで、ストレージコンテナーがクラスター内のすべてのノードで使用できるようになります。

   AHVでは、ハイパーバイザーは、使用中の各vDiskに対してDSFへの個別のiSCSI接続を作成します。
   ESXi 環境では、各 **Storage Container** は、NFS データストアとしてハイパーバイザーに自動的にマウントされます。
   同様に、Hyper-V環境では、各**Storage Container**はSMBデータストアとして表示されます。

   .. note::

     Prismにおける **Storage Containers** の表示例

     .. figure:: images/nutanix_tech_overview_13.png

     vCenterにおける **Storage Containers** (datastores) の表示例

     .. figure:: images/nutanix_tech_overview_14.png

   **Storage Pool** の容量を全て共有する異なるポリシーを持つコンテナを複数作成することが出来ます。

   例えば、フルクローンの永続的な仮想デスクトップに使用されるストレージコンテナに対して  `Deduplication <https://nutanixbible.com/#anchor-book-of-acropolis-elastic-dedupe-engine>`_を有効にしたいと思うかもしれませんが、データベースのようなワークロードには重複排除は意味がありません。

   同様に、バックアップやセキュリティフッテージなどのアーカイブデータ用に `erasure coding <https://nutanixbible.com/#anchor-book-of-acropolis-erasure-coding>`_ を有効にしたストレージコンテナを作成したい場合もあるでしょう。


#. コンテナ構成を更新することで、構成の基本をさらに掘り下げてみましょう。ワークロードが混在した状態で実行されているクラスタ上の重要な VM のキャパシティの可用性を確保するにはどうすればよいでしょうか？

#. 別のストレージコンテナーを選択して、画面下部の **Storage Container Details** を確認しましょう。

   .. figure:: images/storage_config_04.png

   このビューには、利用可能な各データ削減オプションによる節約額の内訳と、コンテナの**Effective Usable Capacity** が表示されます。
   詳細については項目にマウスを重ねてください。
   **Data Reduction Ratio** はデータ圧縮、重複排除、イレイジャーコーディングのみを考慮したデータ効率です。）
   **Overall Efficiency** はDSFのネイティブデータ回避(シン・プロビジョニングやインテリジェント・クローニング)のデータ効率に沿います。

   .. note::

      RF2やRF3構成でNutanixが提供できる論理ストレージの量がどう決まるのか、その違いに興味がありますか? Nutanix Storage Calculatorについてのリンクをチェックしてください。`Nutanix Storage Calculator <https://services.nutanix.com/#/storage-capacity-calculator>`_

Redundancy Factor (RF) (冗長係数)
.................

Distributed Storage Fabricは、従来のRAID技術ではなく、データ保護にレプリケーションファクター(RF)アプローチを使用します。
デフォルトでは、Nutanixストレージへの書き込みは、単一ノードの障害にも耐えられるように、データの2つのコピーを作成します - これは **RF2** と呼ばれています。非常に大規模なクラスタや重要なワークロードの場合、Nutanixは2つのノード障害に耐えられる能力を持ったデータの3つのコピーを書き込むことができます - これは **RF3** と呼ばれています。

RFの書き込みと読み取りの仕組みについて知りたいですか？ 下のビデオをチェックしてください！

.. raw:: html

   <iframe width="640" height="360" src="https://www.youtube.com/embed/OWhdo81yTpk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

RFポリシーは、Prism Element内のストレージコンテナーごとに適用されます。

Block Awarenessは、十分に大規模なクラスタでは、データのセカンダリコピーがプライマリコピーと同じ物理的筐体内のノードに書き込まれないようにします
これにより、マルチノードブロックのデータ損失を回避することができます。
同じコンセプトは、複数のラックにまたがるNutanixクラスタを使用して適用することができます。

ラック/ブロック・フォルトトレランスの基本的な要件は、メタデータの3つのコピーを保存する必要があるため、クラスタ内に最低3つのブロックがあることです（RF2の場合）。 AOS 5.8からは、イレイジャーコーディングを有効にしてラックとブロックの認識をサポートすることができます。

#. **Prism > Home** と戻り  の **Data Resiliency Status**  ボックスの **OK** をクリックします。

.. figure:: images/storage_config_03.png

  Data Resiliency Statusは、クラスタに影響を与えることなく、どれだけの障害を許容できるかを示します。
  リストされた各サービスは、クラスタ内で特定の機能を持っています。
  例えば、Zookeeper ノードはクラスタの設定データ (サービスの状態、IP、ホスト情報など) を維持します。

#. Prism ElementのRF設定は :fa:`cog` メニューの **Redundancy State** から設定出来ます。

   .. note::

     この演習では、冗長計数を2に設定したままにします。

   RF2 クラスターは、RF3 をサポートするようにアップグレードすることができます（最低 5ノード必要です）
   クラスタがRF3用に構成されている場合、個々のストレージコンテナがRF2またはRF3として構成されているかどうかにかかわらず、すべてのデータに対してメタデータのコピーが5つ作成されます

まとめ
+++++++++

- Distributed Storage Fabricは、クラスターにRF2またはRF3共有ストレージを提供します。

- Storage Containersでは、RFレベル、圧縮、重複排除、消去コーディングなど、VMのストレージポリシーを定義することができます
