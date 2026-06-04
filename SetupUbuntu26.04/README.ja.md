[English](README.md) | [Tiếng Việt](README.vi.md) | [日本語](README.ja.md)

# Ubuntu 26.04 のインストール

## 概要

このドキュメントは、ローカルPCにUbuntu 26.04 (Desktop版とServer版) OSとアプリケーションをインストールすることに関するメモです。

* ハードディスクを暗号化してOSをインストールする。
* アプリケーションのインストールとセットアップを行う。

## ハードディスクを暗号化してOSをインストールする

**autoinstall.yaml** を使用して、Ubuntu を自動的にインストールします。

* autoinstall.yaml の使用は難しい経験でした。最終的に最小限の構成になりました。
* Ubuntu 26.04 での自動インストールは、Ubuntu 24.04 とは異なります。
  * Ubuntu 26.04 では、autoinstall.yaml ファイルをインストーラーUSBに入れるだけで、Ubuntu インストーラーが自動的にそれを見つけ、使用するかどうかを尋ねます（自動的に実行するために autoinstall ブートオプションを追加することもできます）。
  * Ubuntu 24.04 では、*user-data* ファイル（autoinstall.yaml と同じ）と空の *meta-data* ファイルを作成してUSBに入れ、USBから起動するときに *autoinstall ds=nocloud;s=/cdrom/* オプションを追加する必要があります。

[autoinstall.yaml](autoinstall.yaml) の例では、3つのパーティションを作成し、未割り当て領域を残しています。
| No. | マウント     | サイズ | 説明                                                                                                                                                           |
| --- | ----------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | /boot/efi   | 1 GB   | GRUB をインストール                                                                                                                                           |
| 2   | /           | 200 GB | Ubuntu ルートをインストール                                                                                                                                   |
| 3   | /home       | 630 GB | /home を保存し、LUKS で暗号化                                                                                                                                |
| 4   | 未割り当て    | 100 GB | [SSD ガベージコレクション](https://umbalaconmeogia.wordpress.com/2026/05/05/tai-sao-nen-chua-ra-10-unallocated-space-tren-ssd/) のために 100 GB を未割り当てのままにします |

この autoinstall.yaml を使用するには、3つの値を設定する必要があります。
1. SSD のシリアル番号（インストーラーがフォーマットする正確なデバイスを見つけるのに役立ちます）。
2. /home にマウントするパーティションを暗号化するためのパスワード。
3. Ubuntu ユーザーのハッシュ化されたパスワード。ハッシュを作成するには、`openssl passwd -6 'your_password_here'` コマンドを使用します。

## アプリケーションのインストールとセットアップ

Ubuntu 26.04 環境のセットアップに関するメモについては、SetupUbuntu26.04.md を参照してください。
