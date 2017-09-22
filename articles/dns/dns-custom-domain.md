---
title: Integrare il servizio DNS di Azure con le risorse di Azure | Microsoft Docs
description: Informazioni su come usare il servizio DNS di Azure per specificare il DNS per le risorse di Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 41c1649bfff035bc641d7c1f5d7803cd105e8297
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure

Il servizio DNS di Azure consente di specificare il DNS per un dominio personalizzato per tutte le risorse di Azure che supportano i domini personalizzati o dispongono di un nome di dominio completo (FQDN). Ad esempio, si ha un'app Web di Azure e si vuole che gli utenti accedano usando contoso.com o www.contoso.com come FQDN. Questo articolo illustra la configurazione del servizio di Azure con il DNS di Azure per l'uso di domini personalizzati.

## <a name="prerequisites"></a>Prerequisiti

Per poter usare il servizio DNS di Azure per il dominio personalizzato, è prima necessario delegare il dominio al servizio DNS di Azure. Per istruzioni sulla configurazione dei server dei nomi per la delega, vedere [Delegare un dominio al servizio DNS di Azure](./dns-delegate-domain-azure-dns.md). Dopo aver delegato il dominio alla zona DNS di Azure, è possibile configurare i record DNS necessari.

È possibile configurare un dominio personale o personalizzato per le [app per le funzioni di ](#azure-function-app), [Azure IoT](#azure-iot), gli [indirizzi IP pubblici](#public-ip-address), le [app Web del servizio app](#app-service-web-apps), l'[archivio BLOB](#blob-storage) e la [rete CDN di Azure](#azure-cdn).

## <a name="azure-function-app"></a>App per le funzioni di Azure

Per configurare un dominio personalizzato per le app per le funzioni di Azure, viene creato un record CNAME e una configurazione nella stessa app per le funzioni.
 
Passare ad **Altro** > **App per le funzioni** e selezionare l'app per le funzioni. Fare clic su **Funzionalità della piattaforma** e in **RETE** fare clic su **Domini personalizzati**.

![pannello App per le funzioni](./media/dns-custom-domain/functionapp.png)

Si noti l'URL corrente nel pannello **Domini personalizzati**, questo indirizzo viene usato come alias per il record DNS creato.

![pannello Domini personalizzati](./media/dns-custom-domain/functionshostname.png)

Passare alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.

|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per il nome di dominio personalizzato.        |
|Tipo     | CNAME        | L'uso di un record CNAME equivale a usare un alias.        |
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Alias     | adatumfunction.azurewebsites.net        | Nome DNS per cui si crea l'alias, in questo esempio è il nome DNS adatumfunction.azurewebsites.net specificato per impostazione predefinita per l'app per le funzioni.        |

Tornare all'app per le funzioni, fare clic su **Funzionalità della piattaforma** e in **RETE** fare clic su **Domini personalizzati**, quindi in **Nomi host** fare clic su **+ Aggiungi il nome host**.

Nel pannello **Aggiungi il nome host** immettere il record CNAME nel campo di testo **nome host** e fare clic su **Convalida**. Se il record è stato trovato, viene visualizzato il pulsante **Aggiungi il nome host**. Fare clic su **Aggiungi il nome host** per aggiungere l'alias.

![pannello Aggiungi il nome host dell'app per le funzioni](./media/dns-custom-domain/functionaddhostname.png)

## <a name="azure-iot"></a>Azure IoT

Per Azure IoT non sono necessarie personalizzazioni nel servizio stesso. Per usare un dominio personalizzato con un hub IoT è necessario unicamente un record CNAME che punta alle risorse.

Passare a **Internet delle cose** > **Hub IoT** e selezionare l'hub IoT. Nel pannello **Panoramica** prendere nota del nome di dominio completo dell'hub IoT.

![Pannello Hub IoT](./media/dns-custom-domain/iot.png)

Passare quindi alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.


|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | myiothub        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per l'hub IoT.        |
|Tipo     | CNAME        | L'uso di un record CNAME equivale a usare un alias.
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Alias     | adatumIOT.azure-devices.net        | Nome DNS si crea l'alias, in questo esempio è il nome host adatumIOT.azure devices.net specificato dall'hub IoT.

Dopo aver creato il record, testare la risoluzione dei nomi con il record CNAME usando `nslookup`

## <a name="public-ip-address"></a>Indirizzo IP pubblico

Per configurare un dominio personalizzato per i servizi che usano una risorsa indirizzo IP pubblico, ad esempio il gateway applicazione, Azure Load Balancer, il servizio cloud, le macchine virtuali di Azure Resource Manager e le macchine virtuali classiche, viene usato un record CNAME.

Passare a **Rete** > **Indirizzo IP pubblico**, selezionare la risorsa indirizzo IP pubblico e fare clic su **Configurazione**. Prendere nota dell'indirizzo IP indicato.

![pannello Indirizzo IP pubblico](./media/dns-custom-domain/publicip.png)

Passare alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.


|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | mywebserver        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per il nome di dominio personalizzato.        |
|Tipo     | Una         | Usare un record A perché la risorsa è un indirizzo IP.        |
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Indirizzo IP     | <your ip address>       | Indirizzo IP pubblico.|

![creare un record A](./media/dns-custom-domain/arecord.png)

Dopo aver creato il record A, eseguire `nslookup` per convalidare la risoluzione del record.

![ricerca DNS IP pubblico](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Web del servizio app

La procedura seguente consente di configurare un dominio personalizzato per un'app Web del servizio app.

Passare a **Web e dispositivi mobili** > **Servizio app** e selezionare la risorsa per cui si sta configurando un nome di dominio personalizzato, quindi fare clic su **Domini personalizzati**.

Si noti l'URL corrente nel pannello **Domini personalizzati**, questo indirizzo viene usato come alias per il record DNS creato.

![pannello Domini personalizzati](./media/dns-custom-domain/url.png)

Passare alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.


|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | mywebserver        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per il nome di dominio personalizzato.        |
|Tipo     | CNAME        | L'uso di un record CNAME equivale a usare un alias. Se la risorsa ha usato indirizzo IP, viene usato un record A.        |
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Alias     | webserver.azurewebsites.net        | Nome DNS per cui si crea l'alias, in questo esempio è il nome DNS webserver.azurewebsites.net specificato per impostazione predefinita per l'app Web.        |


![creare un record CNAME](./media/dns-custom-domain/createcnamerecord.png)

Tornare al servizio app configurato per il nome di dominio personalizzato. Fare clic su **Domini personalizzati** e quindi su **Nomi host**. Per aggiungere il record CNAME creato, fare clic su **+ Aggiungi il nome host**.

![Figura 1](./media/dns-custom-domain/figure1.png)

Al termine del processo, eseguire **nslookup** per verificare il funzionamento della risoluzione dei nomi.

![Figura 1](./media/dns-custom-domain/finalnslookup.png)

Per altre informazioni sul mapping di un dominio personalizzato al servizio app, vedere [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Se è necessario acquistare un dominio personalizzato, vedere [Acquistare un nome di dominio personalizzato per app Web Azure](../app-service/custom-dns-web-site-buydomains-web-app.md) per altre informazioni sui domini del servizio app.

## <a name="blob-storage"></a>Archivio BLOB

La procedura seguente consente di configurare un record CNAME per un account di archiviazione BLOB con il metodo asverify. Questo metodo permette di azzerate il tempo di inattività.

Passare ad **Archiviazione** > **Account di archiviazione**, selezionare l'account di archiviazione e fare clic su **Dominio personalizzato**. Prendere nota del nome di dominio completo nel passaggio 2, questo valore viene usato per creare il primo record CNAME

![dominio personalizzato dell'archivio BLOB](./media/dns-custom-domain/blobcustomdomain.png)

Passare alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.


|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per il nome di dominio personalizzato.        |
|Tipo     | CNAME        | L'uso di un record CNAME equivale a usare un alias.        |
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Nome DNS per cui si crea l'alias, in questo esempio è il nome DNS asverify.adatumfunctiona9ed.blob.core.windows.net specificato per impostazione predefinita per l'account di archiviazione.        |

Tornare all'account di archiviazione facendo clic su **Archiviazione** > **Account di archiviazione**, selezionare l'account di archiviazione e fare clic su **Dominio personalizzato**. Digitare l'alias creato senza il prefisso asverify nella casella di testo, selezionare **Usa convalida CNAME indiretta e fare clic su **Salva**. Al termine di questo passaggio, tornare alla zona DNS e creare un record CNAME senza il prefisso asverify.  A questo punto sarà possibile eliminare il record CNAME con il prefisso cdnverify.

![dominio personalizzato dell'archivio BLOB](./media/dns-custom-domain/indirectvalidate.png)

Per convalidare la risoluzione del DNS, eseguire `nslookup`.

Per altre informazioni sul mapping di un dominio personalizzato a un endpoint di archiviazione BLOB, vedere [Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json).

## <a name="azure-cdn"></a>Rete CDN di Azure

La procedura seguente consente di configurare un record CNAME per un endpoint di rete CDN con il metodo cdnverify. Questo metodo permette di azzerate il tempo di inattività.

Passare a **Rete** > **Profili rete CDN**, selezionare il profilo di rete CDN e fare clic su **Endpoint** in **Generale**.

Selezionare l'endpoint che si sta usando e fare clic su **+ Dominio personalizzato**. Prendere nota del **nome host dell'endpoint**, perché questo valore è il record a cui punta il record CNAME.

![dominio personalizzato della rete CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Passare alla zona DNS e fare clic su **+ Set di record**. Inserire le informazioni seguenti nel pannello **Aggiungi set di record** e fare clic su **OK** per crearlo.

|Proprietà  |Valore  |Descrizione  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Insieme all'etichetta del nome di dominio, questo valore costituisce il dome di dominio completo (FQDN) per il nome di dominio personalizzato.        |
|Tipo     | CNAME        | L'uso di un record CNAME equivale a usare un alias.        |
|TTL     | 1        | 1 corrisponde a 1 ora        |
|Unità TTL     | Ore        | Le ore vengono usate per misurare il tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Nome DNS per cui si crea l'alias, in questo esempio è il nome DNS cdnverify.adatumcdnendpoint.azureedge.net specificato per impostazione predefinita per l'account di archiviazione.        |

Tornare all'endpoint della rete CDN facendo clic su **Rete** > **Profili rete CDN** e selezionare il profilo di rete CDN. Fare clic su **+ Dominio personalizzato**, immettere l'alias del record CNAME senza il prefisso cdnverify e fare clic su **Aggiungi**.

Al termine di questo passaggio, tornare alla zona DNS e creare un record CNAME senza il prefisso cdnverify.  A questo punto sarà possibile eliminare il record CNAME con il prefisso cdnverify. Per altre informazioni sulla rete CDN e su come configurare un dominio personalizzato senza il passaggio di registrazione intermedio, vedere [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare il DNS inverso per i servizi ospitati in Azure](dns-reverse-dns-for-azure-services.md).
