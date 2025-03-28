---
title: Azure Blueprints の概要
description: Azure Blueprints サービスによって Azure 環境でのアーティファクトの作成、定義、デプロイがどのように実現されるかについて理解します。
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919344"
---
# <a name="what-is-azure-blueprints"></a>Azure Blueprints とは

設計図によってエンジニアやアーキテクトがプロジェクト設計パラメーターの概略を示すのと同じように、Azure Blueprints によってクラウド アーキテクトや中央の情報技術部門は、組織の標準、パターン、要件を実装および順守した反復可能な一連の Azure リソースを定義できます。 Azure Blueprints を使用すると、開発チームは新しい環境を迅速に構築して立ち上げることができます。新しい環境は組織のコンプライアンスに従って構築され、ネットワークなどの一連の組み込みコンポーネントを含んでいるという確信が得られるため、開発とデリバリーにかかる時間を短縮できます。

ブループリントは、さまざまなリソース テンプレートやその他のアーティファクトのデプロイを宣言によって調整する手法です。

- ロールの割り当て
- ポリシーの割り当て
- Azure Resource Manager テンプレート (ARM テンプレート)
- リソース グループ

Azure Blueprints サービスの背後には、グローバルに分散された [Azure Cosmos DB](../../cosmos-db/introduction.md) があります。 Blueprint オブジェクトは複数の Azure リージョンにレプリケートされます。 Azure Blueprints でどのリージョンにリソースがデプロイされても、このレプリケーションによって、ブループリント オブジェクトへのアクセスの一貫性、高可用性、短い待ち時間が実現されます。

## <a name="how-its-different-from-arm-templates"></a>ARM テンプレートとの違い

このサービスは、"_環境の設定_" が容易になるように設計されています。 この設定は、多くの場合、一連のリソース グループ、ポリシー、ロールの割り当て、および ARM テンプレートのデプロイから成ります。 ブループリントは、これら個々の "_アーティファクト_" の種類をまとめたパッケージです。ブループリントにより、そのパッケージの作成とバージョン管理を行うことができます (CI/CD (継続的インテグレーションと継続的デリバリー) パイプラインを使用して行うこともできます)。 最終的には、それぞれが、監査と追跡が可能なサブスクリプションに 1 回の操作で割り当てられます。

Azure Blueprints のデプロイに含めるものはほぼすべて、ARM テンプレートを使用して実現できます。 しかし、ARM テンプレートは Azure にネイティブに存在するドキュメントではありません。それぞれローカルに、またはソース コントロール内に格納されています。 テンプレートは 1 つ以上の Azure リソースのデプロイに使用されますが、それらのリソースがデプロイされると、テンプレートとのアクティブな結び付きや関係は失われます。

Azure Blueprints では、ブループリント定義 (何をデプロイする "_必要がある_" か) とブループリント割り当て (何がデプロイ "_された_" か) の間の関係が維持されます。 この結び付きによって、デプロイの追跡と監査が向上します。 また、Azure Blueprints は、同じブループリントで管理されている複数のサブスクリプションを一度にアップグレードすることもできます。

ARM テンプレートとブループリントのどちらかを選ぶ必要はありません。 各ブループリントは、0 個以上の ARM テンプレート _アーティファクト_ で構成することができます。 このサポートは、ARM テンプレートを開発し、そのライブラリを維持するために以前行った作業を、Azure Blueprints で再利用できることを意味します。

## <a name="how-its-different-from-azure-policy"></a>Azure Policy との違い

ブループリントは、一貫性とコンプライアンスを確保する再利用可能な Azure クラウド サービス、セキュリティ、デザインの実装に関連した、焦点を定めた一連の標準、パターン、要件を作成するためのパッケージまたはコンテナーです。

[ポリシー](../policy/overview.md)は、デプロイ時のリソース プロパティと、既に存在するリソースのリソース プロパティに焦点を合わせた、既定での許可と明示的な拒否のシステムです。 サブスクリプションに含まれるリソースが要件と標準に準拠していることを確認することによって、クラウドのガバナンスを支援します。

ブループリントにポリシーを含めると、ブループリントの割り当て時に、適切なパターンや設計を作成できます。 このポリシー追加により、承認済みまたは予想されている変更しか環境に対して行えないことが保証され、ブループリントの意図に対する継続的なコンプライアンスが確保されます。

ポリシーは、数多くの "_アーティファクト_" の 1 つとしてブループリント定義に含めることができます。 また、ブループリントでは、ポリシーおよびイニシアティブでパラメーターを使用することもできます。

## <a name="blueprint-definition"></a>ブループリント定義

ブループリントは "_アーティファクト_" で構成されます。 Azure Blueprints は、現在、次のリソースをアーティファクトとしてサポートしています。

|リソース  | 階層のオプション| 説明  |
|---------|---------|---------|
|リソース グループ | サブスクリプション | ブループリント内の他のアーティファクトで使用する新しいリソース グループを作成します。  プレースホルダーであるこれらのリソース グループを使用すると、リソースの構造を正確に希望したとおりに編成し、含めたポリシーとロールの割り当てのアーティファクトおよび ARM テンプレートについて、スコープを制限する指定ができます。 |
|ARM テンプレート | サブスクリプション、リソース グループ | 入れ子になったテンプレートやリンクされたテンプレートを含むテンプレートを使用して、複雑な環境を構築します。 たとえば、SharePoint ファーム、Azure Automation State Configuration、Log Analytics ワークスペースの環境が該当します。 |
|ポリシーの割り当て | サブスクリプション、リソース グループ | ブループリントの割り当て先であるサブスクリプションに、ポリシーまたはイニシアティブを割り当てることができます。 ポリシーまたはイニシアティブは、ブループリント定義の場所のスコープ内にある必要があります。 ポリシーまたはイニシアティブにパラメーターが含まれる場合、これらのパラメーターは、ブループリントを作成する時点で割り当てるか、ブループリント割り当て時に割り当てられます。 |
|ロールの割り当て | サブスクリプション、リソース グループ | 既存のユーザーまたはグループを組み込みのロールに追加して、リソースに対して常に適切なユーザーが確実に適切なアクセス権を持つようにすることができます。 ロールの割り当ては、サブスクリプション全体に対して定義することも、ブループリントに含まれる特定のリソース グループに対して入れ子にすることもできます。 |

### <a name="blueprint-definition-locations"></a>ブループリント定義の場所

ブループリント定義を作成するとき、ブループリントの保存場所を定義します。 ブループリントは、自分が **共同作成者** のアクセス権を持っている [管理グループ](../management-groups/overview.md)またはサブスクリプションに保存できます。 保存場所が管理グループである場合、その管理グループのすべての子サブスクリプションへの割り当てにブループリントを使用できます。

### <a name="blueprint-parameters"></a>ブループリントのパラメーター

ブループリントでは、ポリシーやイニシアティブまたは ARM テンプレートにパラメーターを渡すことができます。 いずれかの "_アーティファクト_" がブループリントに追加されるときに、作成者は個々のブループリント割り当てに対して、定義済みの値を指定するか、割り当て時に値を指定できるようにするかを決定します。 この柔軟さによって、ブループリントのすべての使用に対して事前に決められた値を定義するか、割り当ての時点で値を決定できるようにするかを選ぶことができます。

> [!NOTE]
> ブループリントには、そのブループリント専用のパラメーターを設定できますが、現時点では、このようなパラメーターを作成できるのはブループリントがポータルではなく REST API から生成されている場合のみです。

詳細については、[ブループリントのパラメーター](./concepts/parameters.md)に関する記事をご覧ください。

### <a name="blueprint-publishing"></a>ブループリントの発行

ブループリントを作成したとき、最初は **ドラフト** モードであると見なされます。 割り当てができる状態になったら、**発行する** 必要があります。 発行するには、**バージョン** 文字列 (文字、数字、ハイフンを使用し、長さは最大 20 文字) を、**変更に関するメモ** (オプション) と共に定義する必要があります。 **バージョン** によって、同じブループリントに対する今後の変更が区別され、各バージョンを割り当てることが可能になります。 これはまた、同じブループリントの異なる複数の **バージョン** を同じサブスクリプションに割り当てできることを意味します。 ブループリントに追加の変更を加えた場合は、**発行されていない変更** に加えて、**発行済み**
**バージョン** も引き続き存在します。 変更が完了すると、更新されたブループリントは、新しい一意の **バージョン** を持つ **発行済み** のブループリントとして、やはり割り当て可能になります。

## <a name="blueprint-assignment"></a>ブループリント割り当て

ブループリントの個々の **発行済み** **バージョン** を既存の管理グループまたはサブスクリプションに割り当てることができます (名前の最大長は 90 文字)。 ポータルにおいて、ブループリントの既定の **バージョン** は、直近の **発行済み** になったバージョンになります。 アーティファクトのパラメーターまたはブループリントのパラメーターがある場合、そのパラメーターは割り当て処理時に定義されます。

> [!NOTE]
> ブループリント定義を管理グループに割り当てると、割り当てオブジェクトが管理グループに存在することになります。 アーティファクトの配置は、現在もサブスクリプションを対象としています。 管理グループへの割り当てを実行するには、[作成または更新 REST API](/rest/api/blueprints/assignments/createorupdate) を使用する必要があり、要求本文には、ターゲット サブスクリプションを定義する `properties.scope` の値が含まれている必要があります。

## <a name="permissions-in-azure-blueprints"></a>Azure Blueprints におけるアクセス許可

ブループリントを使用するには、[Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md) (Azure RBAC) を通じてアクセス許可を得る必要があります。 Azure portal でブループリントを読み取るまたは表示するには、ブループリント定義が配置されているスコープへの読み取りアクセス権がアカウントに必要です。

ブループリントを作成するには、お使いのアカウントに次のアクセス許可が必要です。

- `Microsoft.Blueprint/blueprints/write` - ブループリント定義の作成
- `Microsoft.Blueprint/blueprints/artifacts/write` - ブループリント定義でのアーティファクトの作成
- `Microsoft.Blueprint/blueprints/versions/write` - ブループリントの発行

ブループリントを削除するには、お使いのアカウントに次のアクセス許可が必要です。

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> ブループリント定義のアクセス許可は、その保存先となる管理グループまたはサブスクリプションのスコープで付与または継承する必要があります。

ブループリントを割り当てまたは割り当て解除するには、お使いのアカウントに次のアクセス許可が必要です。

- `Microsoft.Blueprint/blueprintAssignments/write` - ブループリントの割り当て
- `Microsoft.Blueprint/blueprintAssignments/delete` - ブループリントの割り当て解除

> [!NOTE]
> サブスクリプションにブループリント割り当てが作成されると、ブループリント割り当てと割り当て解除のアクセス許可がサブスクリプションのスコープに対して付与されるか、サブスクリプション スコープに継承されます。

以下の組み込みロールを使用できます。

|Azure ロール | 説明 |
|-|-|
|[所有者](../../role-based-access-control/built-in-roles.md#owner) | 他のアクセス許可に加えて、すべての Azure Blueprints 関連のアクセス許可が含まれます。 |
|[Contributor](../../role-based-access-control/built-in-roles.md#contributor) | 他のアクセス許可に加えて、ブループリント定義を作成および削除できますが、ブループリントの割り当てのアクセス許可は持っていません。 |
|[ブループリント共同作成者](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | ブループリントの定義を管理できますが、それらを割り当てることはできません。 |
|[ブループリント オペレーター](../../role-based-access-control/built-in-roles.md#blueprint-operator) | 既存の発行済みのブループリントを割り当てることはできますが、新しいブループリント定義は作成できません。 ブループリントの割り当ては、ユーザーによって割り当てられたマネージド ID を使用して割り当てが行われた場合にのみ機能します。 |

これらの組み込みロールが自分のセキュリティ ニーズに適合しない場合は、[カスタム ロール](../../role-based-access-control/custom-roles.md)を作成することを検討してください。

> [!NOTE]
> システム割り当てのマネージド ID を使用している場合、Azure Blueprints のサービス プリンシパルがデプロイを有効にするには、割り当てられたサブスクリプションに対して **所有者** ロールが必要です。 ポータルを使用する場合、デプロイに対するこのロールの付与と取り消しは自動的に行われます。 REST API を使用する場合はこのロールを手動で付与する必要がありますが、取り消しはデプロイ完了後に自動的に行われます。 ユーザー割り当てマネージド ID を使用する場合は、ブループリント割り当てを作成するユーザーのみに `Microsoft.Blueprint/blueprintAssignments/write` アクセス許可が必要です。これは、**所有者** と **ブループリント オペレーター** の組み込みロールの両方に含まれています。

## <a name="naming-limits"></a>名前付けの制限

特定のフィールドでは、次の制限があります。

|Object|フィールド|使用できる文字|最大 長さ|
|-|-|-|-|
|ブループリント|名前|英字、数字、ハイフン、およびピリオド|48|
|ブループリント|Version|英字、数字、ハイフン、およびピリオド|20|
|ブループリント割り当て|名前|英字、数字、ハイフン、およびピリオド|90|
|ブループリント アーティファクト|名前|英字、数字、ハイフン、およびピリオド|48|

## <a name="video-overview"></a>ビデオの概要

Azure Blueprints に関する次の概要は、Azure Fridays のものです。 ビデオのダウンロードについては、Channel 9 の「[Azure Fridays - An overview of Azure Blueprints (Azure Fridays - Azure Blueprints の概要)](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints)」をご覧ください。

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>次のステップ

- [ブループリントの作成 - ポータル](./create-blueprint-portal.md)。
- [ブループリントの作成 - PowerShell](./create-blueprint-powershell.md)。
- [ブループリントの作成 - REST API](./create-blueprint-rest-api.md)。
