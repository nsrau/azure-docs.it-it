---
title: Eseguire una distribuzione in una rete virtuale esistente
description: Viene descritto come consentire agli utenti dell'applicazione gestita di selezionare una rete virtuale esistente. La rete virtuale può trovarsi all'esterno dell'applicazione gestita.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260684"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Usare la rete virtuale esistente con le applicazioni gestite di Azure

Questo articolo illustra come definire un'applicazione gestita di Azure che si integra con una rete virtuale esistente nella sottoscrizione del consumer. L'applicazione gestita consente al consumer di decidere se creare una nuova rete virtuale o utilizzarne una esistente. La rete virtuale esistente può essere esterna al gruppo di risorse gestite.

## <a name="main-template"></a>Modello principale

Per prima cosa, esaminiamo il **mainTemplate.jssu** file. L'intero modello per la distribuzione di una macchina virtuale e delle risorse associate è illustrato di seguito. Successivamente, si esamineranno più accuratamente le parti del modello correlate all'uso di una rete virtuale esistente.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Si noti che la rete virtuale è [distribuita in modo condizionale](../templates/conditional-resource-deployment.md). Il consumer passa un valore di parametro che indica se creare una nuova rete virtuale o utilizzarne una esistente. Se il consumer seleziona una nuova rete virtuale, la risorsa viene distribuita. In caso contrario, la risorsa viene ignorata durante la distribuzione.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

La variabile per l'ID rete virtuale ha due proprietà. Una proprietà restituisce l'ID risorsa quando viene distribuita una nuova rete virtuale. L'altra proprietà restituisce l'ID risorsa quando viene usata una rete virtuale esistente. L'ID risorsa per la rete virtuale esistente include il nome del gruppo di risorse che contiene la rete virtuale.

L'ID subnet viene costruito dal valore dell'ID rete virtuale. Usa il valore corrispondente alla selezione dei consumer.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

L'interfaccia di rete è impostata sulla variabile ID subnet.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definizione dell'interfaccia utente

A questo punto, esaminiamo il **createUiDefinition.jssu** file. L'intero file è:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Il file include un elemento rete virtuale.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Tale elemento consente al consumer di selezionare una rete virtuale nuova o esistente.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Rete virtuale nuova o esistente":::

Negli output si include un valore che indica se il consumer ha selezionato una rete virtuale nuova o esistente. Esiste anche un valore Identity gestito.

> [!NOTE]
> Il valore di output per l'identità gestita deve essere denominato **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione del file di definizione dell'interfaccia utente, vedere [CreateUiDefinition.json per l'esperienza di creazione di un'applicazione gestita di Azure](create-uidefinition-overview.md).
