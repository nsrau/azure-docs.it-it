---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971317"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **+Crea una risorsa** > **Contenuti multimediali** > **Servizi multimediali**.
1. Nella sezione **Creare un account Servizi multimediali** immettere i valori necessari.

    | Nome | Descrizione |
    | ---|---|
    |**Nome account**|Immettere il nome del nuovo account di Servizi multimediali. Un nome di account di Servizi multimediali deve essere composto solo da lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.|
    |**Sottoscrizione**|Se sono disponibili più sottoscrizioni, selezionarne una dall'elenco di sottoscrizioni di Azure a cui si è autorizzati ad accedere.|
    |**Gruppo di risorse**|Selezionare la risorsa nuova o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../../../azure-resource-manager/management/overview.md#resource-groups) per altre informazioni.|
    |**Posizione**|Selezionare l'area geografica che verrà usata per archiviare i contenuti multimediali e i record di metadati per l'account Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. |
    |**Storage Account**|Selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account di Servizi multimediali oppure è possibile crearne uno nuovo. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.<br/><br/>È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. È possibile usare il portale di Azure per aggiungere account di archiviazione secondari. Per altre informazioni, vedere [Account di archiviazione di Azure con account di Servizi multimediali di Azure](../storage-account-concept.md).<br/><br/>L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.|
    |**Impostazioni avanzate**| È possibile creare un account usando un'identità gestita dal sistema selezionando il pulsante di opzione **Gestita dal sistema**.  Se si seleziona questa opzione, sarà possibile usare le chiavi gestite dal cliente o BYOK (Bring Your Own Key) e Servizi multimediali per abilitare l'archiviazione attendibile.  Per altre informazioni sulle chiavi gestite dal cliente, vedere [Chiavi BYOK (chiavi gestite dal cliente) con Servizi multimediali](../concept-use-customer-managed-keys-byok.md). Verranno inoltre abilitate le [identità gestite](../concept-managed-identities.md).

1. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
1. Fare clic su **Crea** nella parte inferiore del form.
