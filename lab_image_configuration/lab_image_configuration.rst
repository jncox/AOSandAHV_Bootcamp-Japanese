.. _lab_image_configuration:


-------------------------
イメージ管理
-------------------------

概要
++++++++

イメージ管理について基本的なことを学びます。

イメージ管理
+++++++++++++++++++

Nutanixのイメージ管理サービスは、ISOファイルや作成したイメージディスクを保存する場所です。(VMwareのテンプレートに似ています。)

仮想マシンのデプロイに使用するCentOSのISOファイルをアップロードしてみましょう。

**Prism Central > Explore** と進み **Images** をクリックします。

次に、**Add Image** と進み、**URL** をクリックします。

以下のフィールドに入力し、**Upload File** をクリックします。

- **Enter Image URL** - http://10.20.134.222/images/CentOS-7-x86_64-Minimal-1804.iso

.. figure:: images/deploy_workloads_01.png

以下のフィールドに入力し、**Upload File** をクリックします。

- **Image Name** - CentOS7-*initials*
- **Image Type** - ISO
- **Image Description** - (Optional) Add a description

.. figure:: images/deploy_workloads_02.png

.. note::
  Prism Centralのイメージ管理では、そのPrism Centralインスタンスに登録されているクラスタにおいて使用できるようにイメージをアップロードすることができます

続いて、Windows 2012のISOをアップロードします。 もし既にアップロードされている場合は不要です。

**Add Image** と進み **URL** をクリックします。

以下のフィールドに入力し、**Upload File** をクリックします。

- **Enter Image URL** - http://10.20.134.222/images/server_2012_r2_vl_x64_dvd_3319595.iso

以下のフィールドに入力し、**Upload File** をクリックします。

- **Image Name** - Windows2012-*initials*
- **Image Type** - ISO
- **Image Description** - (Optional) Add a description

まとめ
+++++++++

イメージはクラスタの Prism Element から直接アップロードすることも可能です。
このツールは、VM ディスクイメージを AHV が理解できる形式に変換する機能を持っています。
raw、vhd、vhdx、vmdk、vdi、iso、およびqcow2ディスクフォーマットをサポートしています。
