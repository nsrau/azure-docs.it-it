---
title: Controllare e gestire la conformità dei criteri
titleSuffix: Azure Machine Learning
description: Informazioni su come usare i criteri di Azure per usare i criteri predefiniti per Azure Machine Learning per assicurarsi che le aree di lavoro siano conformi ai propri requisiti.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 22901c4e8409fc4846c1566a57b2679f4fa92396
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444561"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Controllare e gestire Azure Machine Learning usando criteri di Azure

[Criteri di Azure](../governance/policy/index.yml) è uno strumento di governance che consente di garantire che le risorse di Azure siano conformi ai criteri. Con Azure Machine Learning è possibile assegnare i seguenti criteri:

* **Chiave gestita dal cliente** : controlla o impone se le aree di lavoro devono usare una chiave gestita dal cliente.
* **Collegamento privato** : controllare se le aree di lavoro usano un endpoint privato per comunicare con una rete virtuale.

I criteri possono essere impostati in ambiti diversi, ad esempio a livello di sottoscrizione o di gruppo di risorse. Per ulteriori informazioni, vedere la [documentazione di criteri di Azure](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Criteri predefiniti

Azure Machine Learning fornisce un set di criteri che è possibile utilizzare per gli scenari comuni con Azure Machine Learning. È possibile assegnare queste definizioni dei criteri alla sottoscrizione esistente o utilizzarle come base per creare definizioni personalizzate. Per un elenco completo dei criteri predefiniti per Azure Machine Learning, vedere [criteri predefiniti per Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Per visualizzare le definizioni dei criteri predefinite correlate ai Azure Machine Learning, attenersi alla procedura seguente:

1. Passare a __criteri di Azure__ nella [portale di Azure](https://portal.azure.com).
1. Selezionare le __definizioni__.
1. Per __tipo__ selezionare _predefinito_ e per __categoria__ selezionare __Machine Learning__.

Da qui è possibile selezionare le definizioni dei criteri per visualizzarle. Durante la visualizzazione di una definizione, è possibile usare il collegamento __assign__ per assegnare il criterio a un ambito specifico e configurare i parametri per il criterio. Per altre informazioni, vedere [assegnare un criterio-portale](../governance/policy/assign-policy-portal.md).

È anche possibile assegnare i criteri tramite [Azure PowerShell](../governance/policy/assign-policy-powershell.md), l'interfaccia della riga di comando di [Azure](../governance/policy/assign-policy-azurecli.md)e i [modelli](../governance/policy/assign-policy-template.md).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Crittografia delle aree di lavoro con chiave gestita dal cliente

Controlla se le aree di lavoro devono essere crittografate con una chiave gestita dal cliente o usando una chiave gestita da Microsoft per crittografare le metriche e i metadati. Per altre informazioni sull'uso della chiave gestita dal cliente, vedere la sezione [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) dell'articolo sulla crittografia dei dati.

Per configurare questo criterio, impostare il parametro Effect su __Audit__ o __Deny__. Se è impostato su __Audit__ , è possibile creare aree di lavoro senza una chiave gestita dal cliente e nel log attività viene creato un evento di avviso.

Se il criterio è impostato su __Nega__ , non è possibile creare un'area di lavoro a meno che non specifichi una chiave gestita dal cliente. Il tentativo di creare un'area di lavoro senza una chiave gestita dal cliente genera un errore simile a `Resource 'clustername' was disallowed by policy` e crea un errore nel log attività. L'identificatore dei criteri viene restituito anche come parte di questo errore.

## <a name="workspaces-should-use-private-link"></a>Le aree di lavoro devono usare un collegamento privato

Controlla se le aree di lavoro devono usare il collegamento privato di Azure per comunicare con la rete virtuale di Azure. Per altre informazioni sull'uso del collegamento privato, vedere [configurare un collegamento privato per un'area di lavoro](how-to-configure-private-link.md).

Per configurare questo criterio, impostare il parametro Effect su __Audit__. Se si crea un'area di lavoro senza usare un collegamento privato, nel log attività viene creato un evento di avviso.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Criteri di Azure](../governance/policy/overview.md)
* [Criteri predefiniti per Azure Machine Learning](policy-reference.md)
* [Uso dei criteri di sicurezza con il Centro sicurezza di Azure](../security-center/tutorial-security-policy.md)