---
title: Aggiungere un dominio personalizzato all'endpoint della rete CDN | Microsoft Docs
description: Informazioni su come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: 98d4900e28f1850050dc4fbe1f97435e52afaf08
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Aggiungere un dominio personalizzato all'endpoint della rete CDN
Dopo aver creato un profilo, in genere vengono creati anche uno o più [endpoint](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) della rete CDN, ovvero un sottodominio di `azureedge.net`, per distribuire il contenuto mediante HTTP e HTTPS. Per impostazione predefinita, questo endpoint è incluso in tutti gli URL, ad esempio `https://contoso.azureedge.net/photo.png`. Per comodità, la rete CDN di Azure offre la possibilità di associare un dominio personalizzato, ad esempio `www.contoso.com`, all'endpoint. Con questa opzione, anziché l'endpoint si usa un dominio personalizzato per distribuire il contenuto. Questa opzione è utile se, ad esempio, si desidera che il nome di dominio sia visibile ai clienti per scopi di personalizzazione.

Se non si dispone già di un dominio personalizzato, è prima necessario acquistarne uno con un provider di dominio. Dopo aver acquistato un dominio personalizzato, eseguire la procedura seguente:
1. [Accedere ai record DNS del provider di dominio](#step-1-access-dns-records-by-using-your-domain-provider)
2. [Creare i record DNS CNAME](#step-2-create-the-cname-dns-records)
    - Opzione 1: mapping diretto del dominio personalizzato all'endpoint della rete CDN
    - Opzione 2: mapping del dominio personalizzato all'endpoint della rete CDN tramite cdnverify 
3. [Abilitare il mapping dei record CNAME in Azure](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Passaggio dipendente) Eseguire il mapping del dominio personalizzato permanente all'endpoint della rete CDN](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Passaggio 1: Accedere ai record DNS tramite il provider di dominio

Se si usa Azure per ospitare i [domini DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview), è necessario delegare il DNS del provider di dominio a DNS di Azure. Per altre informazioni, vedere [Delegare un dominio al servizio DNS di Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

In caso contrario, se si usa il provider di dominio per gestire il dominio DNS, eseguire l'accesso al sito Web del provider di dominio. Cercare la pagina per la gestione dei record DNS facendo riferimento alla documentazione del provider oppure eseguendo una ricerca delle aree del sito Web con etichetta come **Nome del dominio**, **DNS** o **Gestione dei server dei nomi**. È spesso possibile visualizzare la pagina dei record DNS visualizzando le informazioni dell'account e cercando un collegamento quale, ad esempio, **Domini personali**. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche**. 


## <a name="step-2-create-the-cname-dns-records"></a>Passaggio 2: Creare i record DNS CNAME

Prima di poter usare un dominio personalizzato con un endpoint della rete CDN di Azure, è necessario creare un record di nome canonico (CNAME) con il provider del dominio. Un record CNAME è un tipo di record nel DNS (Domain Name System) che esegue il mapping di un dominio di origine a un dominio di destinazione, specificando un nome di dominio alias per il nome di dominio "canonico" o true. Per la rete CDN di Azure il dominio di origine è il dominio (e sottodominio) personalizzato, mentre il dominio di destinazione è l'endpoint della rete CDN. La rete CDN di Azure verifica il record DNS CNAME quando il dominio personalizzato viene aggiunto all'endpoint dal portale o dall'API. 

Un record CNAME esegue il mapping di un dominio e un sottodominio specifici, ad esempio `www.contoso.com` o `cdn.contoso.com`. Non è possibile eseguire il mapping di un record CNAME a un dominio radice, ad esempio `contoso.com`. Un sottodominio può essere associato solo a un endpoint della rete CDN e il record CNAME creato instrada all'endpoint specificato tutto il traffico indirizzato al sottodominio. Ad esempio, se si associa `www.contoso.com` all'endpoint della rete CDN, non è possibile associarlo a un altro endpoint di Azure, come un endpoint dell'account di archiviazione o del servizio cloud. Tuttavia, è possibile usare più sottodomini dello stesso dominio per endpoint di servizio diversi. È anche possibile eseguire il mapping di sottodomini diversi allo stesso endpoint della rete CDN.

Per eseguire il mapping del dominio personalizzato a un endpoint della rete CDN, usare una delle opzioni seguenti:

- Opzione 1: mapping diretto. Se nel dominio personalizzato non è in esecuzione alcun traffico di produzione, è possibile eseguire il mapping diretto di un dominio personalizzato a un endpoint della rete CDN. Il processo di mapping del dominio personalizzato all'endpoint della rete CDN può comportare un breve periodo di inattività del dominio durante la relativa registrazione nel portale di Azure. La voce di mapping CNAME deve avere il formato seguente: 
 
  | NOME             | TIPO  | VALORE                  |
  |------------------|-------|------------------------|
  | www\.consoto.com | CNAME | consoto\.azureedge.net |


- Opzione 2: mapping con il sottodominio **cdnverify**. Se nel dominio personalizzato è in esecuzione traffico di produzione che non può essere interrotto, è possibile creare un mapping CNAME temporaneo all'endpoint della rete CDN. Con questa opzione viene usato il sottodominio **cdnverify** di Azure per rendere disponibile un passaggio di registrazione intermedio in modo che gli utenti possano accedere al dominio senza interruzione durante l'esecuzione del mapping DNS.

   1. Creare un nuovo record CNAME e specificare un alias di sottodominio che includa il sottodominio **cdnverify**. Ad esempio, **cdnverify.www** o **cdnverify.cdn**. 
   2. Specificare il nome host, ovvero l'endpoint della rete CDN, nel formato seguente: `cdnverify.<EndpointName>.azureedge.net`. La voce di mapping CNAME deve avere il formato seguente: 

   | NOME                       | TIPO  | VALORE                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.consoto.com | CNAME | cdnverify.consoto\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Passaggio 3: Abilitare il mapping dei record CNAME in Azure

Dopo avere registrato il dominio personalizzato usando una delle procedure precedenti, è possibile abilitare la funzionalità del dominio personalizzato nella rete CDN di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al profilo della rete CDN contenente l'endpoint da mappare a un dominio personalizzato.  
2. Nel pannello **profilo della rete CDN** selezionare l'endpoint della rete CDN a cui si desidera associare il sottodominio.
3. Nella parte superiore sinistra del pannello dell'endpoint fare clic su **Dominio personalizzato**. 

   ![Pulsante Dominio personalizzato](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. Nella casella di testo **Nome host personalizzato** immettere il dominio personalizzato, compreso il sottodominio. Ad esempio, `www.contoso.com` o `cdn.contoso.com`.

   ![Aggiungere una finestra di dialogo del dominio personalizzato](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Fare clic su **Aggiungi**.

   Azure verifica l'esistenza del record CNAME per il nome di dominio immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato. La propagazione del record CNAME nei server dei nomi può richiedere tempo. Se il dominio non viene convalidato immediatamente, verificare la correttezza del record CNAME, attendere alcuni minuti e riprovare. Per gli endpoint della **rete CND di Azure fornita da Verizon** (Standard e Premium) possono essere necessari fino a 90 minuti perché le modifiche al dominio personalizzato vengano propagate a tutti nodi perimetrali della rete CDN.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Passaggio 4: Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN

Dopo aver completato la registrazione del dominio personalizzato, verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN.
 
1. Assicurarsi di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN memorizza nella cache il contenuto in contenitori BLOB pubblici. Per testare il dominio personalizzato, verificare che il contenitore sia impostato in modo da consentire l'accesso pubblico e che contenga almeno un BLOB.

2. Nel browser passare all'indirizzo del BLOB usando il dominio personalizzato. Ad esempio, se il dominio personalizzato è `cdn.contoso.com`, l'URL del BLOB memorizzato nella cache dovrebbe essere simile all'URL seguente: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Passaggio 5 (passaggio dipendente): Eseguire il mapping del dominio personalizzato permanente all'endpoint della rete CDN

Questo passaggio dipende dal passaggio 2, opzione 2 (mapping con il sottodominio **cdnverify**). Se si usa il sottodominio temporaneo **cdnverify** e se è stato verificato il relativo funzionamento, è possibile eseguire il mapping del dominio personalizzato permanente all'endpoint della rete CDN.

1. Nel sito Web del provider di dominio creare un record DNS CNAME per eseguire il mapping del dominio personalizzato permanente all'endpoint della rete CDN. La voce di mapping CNAME deve avere il formato seguente: 
 
   | NOME             | TIPO  | VALORE                  |
   |------------------|-------|------------------------|
   | www\.consoto.com | CNAME | consoto\.azureedge.net |
2. Eliminare il record CNAME con il sottodominio **cdnverify** creato in precedenza.

## <a name="see-also"></a>Vedere anche
[Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure](cdn-create-new-endpoint.md)  
[Delega del dominio al servizio DNS di Azure](../dns/dns-domain-delegation.md)