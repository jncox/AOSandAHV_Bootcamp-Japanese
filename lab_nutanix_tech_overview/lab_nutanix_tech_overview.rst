.. _lab_nutanix_technology_overview:

---------------------------------
Nutanix Tech ラボ
---------------------------------

概要
++++++++

このラボでは、Prism Element UI を紹介し、その全容と使い方を理解します。

Prism Element
+++++++++++++

PrismサービスはNutanixクラスタを管理するためのWeb UIを提供し、すべてのController VM (CVM)上で動作します。
また、Prism Elementと呼ばれるPrism内のサービスは個々のCVMのIPを介して、またはクラスタの仮想IPを介してアクセスすることができ、現在のPrismリーダーにリダイレクトされます。

クラスタIPを使用してNutanix Prism GUIにログインします。推奨ブラウザはGoogle Chorome もしくは Firefoxです。

#. \https://<*NUTANIX-CLUSTER-IP*>:9440 を開きます。

#. 以下の認証情報を用いてログインします。

   - **Username** - admin
   - **Password** - *HPOC Password*

   .. figure:: images/nutanix_tech_overview_01.png

#. Prism Elementにログインしたら、PrismのUIに慣れておきましょう。 ホーム画面や他の画面の情報を確認してください。

#. ホーム画面の以下の項目を確認してください。

   - Hypervisor
   - Version
   - Hardware Model
   - Health
   - VM Summary
   - Warning Alerts
   - Data Resiliency Status

   .. figure:: images/nutanix_tech_overview_02.png

#. **View Options** のプルダウンメニューにUIナビゲーション・オプションがあることを確認してください。 このメニューのラベルは、現在表示されているダッシュボードに基づいて変わります。 たとえば、Homeダッシュボードからは **Home** 、Healthダッシュボードからは **Health** などのラベルが表示されます。

   .. figure:: images/nutanix_tech_overview_03.png

　**Prism > Hardware** でクラスタのハードウェアを調べ、**Hardware** をクリックしてから **Diagram** をクリックします。

#. ハードウェア情報を確認してください。

   - Blocks
   - Hosts
   - Memory
   - CPU
   - Disks

   .. figure:: images/nutanix_tech_overview_04.png

#. 他の項目も目を通しておきましょう。

   - VM
   - Health
   - Network
   - Data Protection
   - Storage
   - Alerts
   - Etc.

#. Prism UIの他のセクション

   - Health :fa:`heartbeat`
   - Alarms :fa:`bell`
   - Tasks :fa:`circle-o`
   - Search :fa:`search`
   - Help :fa:`question`
   - Configuration :fa:`cog`
   - User :fa:`user`

   .. figure:: images/nutanix_tech_overview_05.png

Prism Element UI のレビュー
.......................

実行しているAOSのバージョンはどこから確認できますか？

.. figure:: images/nutanix_tech_overview_06_5-11.png

**User** のドロップダウンメニューから :fa:`user` の **About Nutanix** をクリックすると確認できます。

ホスト（またはノード）の数とリソースの容量と現在の利用率の概要はどこから確認できますか？

.. figure:: images/nutanix_tech_overview_07.png

**Prism > Hardware** と進み、 **Hardware** の **Table** をクリックします。

クラスターの状態を確認するためにはどこから確認できますか？

.. figure:: images/nutanix_tech_overview_08.png

**Prism > Health** と進み、 **Health** の **Summary** から確認できます。

システムの最新のアクティビティはどのページに表示されますか？
このページでは、任意のタスクの進捗状況を監視し、タイムスタンプを使用して過去に何が行われたかを追跡することができます。
2つの異なる方法を考えてみましょう。

.. figure:: images/nutanix_tech_overview_09.png

表示オプションメニューから **Tasks** を選択するか、ツールバーのベルアイコン : fa:`Bell` の右にあるサークル : fa:`circle-o` アイコンをクリックします。

.. note::

  ESXiの場合

  - Prismで作成されたコンテナーは、vCenterでデータストアとして表示されます。

  Prismからのストレージコンテナーの表示例

  .. figure:: images/nutanix_tech_overview_13.png

  vCenterからのストレージコンテナー(データストア)の表示例

  .. figure:: images/nutanix_tech_overview_14.png

まとめ
+++++++++

- Prism は考え抜かれたUIです。
- 重要な情報は中央部分に配置しています。
