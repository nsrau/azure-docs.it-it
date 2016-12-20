---
title: Come eseguire il mapping del contenuto di una rete CDN a un dominio personalizzato | Documentazione Microsoft
description: Questo argomento descrive come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato.
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 567e72805c22c8100ef2d8d97c1e26a77f214ca3


---
# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Come eseguire il mapping di un dominio personalizzato all'endpoint della rete per la distribuzione di contenuti (rete CDN)
È possibile eseguire il mapping di un dominio personalizzato a un endpoint della rete CDN per usare il proprio nome di dominio negli URL del contenuto memorizzato nella cache anziché un sottodominio di azureedge.net.

Per eseguire il mappping del dominio personalizzato a un endpoint della rete CDN, sono disponibili due modi:

1. [Creare un record CNAME con il registrar ed eseguire il mapping del dominio personalizzato e del sottodominio all'endpoint della rete CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)
   
    Un record CNAME è una funzionalità DNS con cui viene eseguito il mapping di un dominio di origine, come `www.contosocdn.com` o `cdn.contoso.com`, a uno di destinazione. In questo caso, il dominio di origine corrisponde al dominio personalizzato e al relativo sottodominio. È sempre necessario un sottodominio, come **www** o **cdn**. Il dominio di destinazione è l'endpoint della rete CDN.  
   
    Il processo di mapping del dominio personalizzato all'endpoint della rete CDN può tuttavia comportare un breve periodo di inattività del dominio durante la relativa registrazione nel portale di Azure.
2. [Aggiungere un passaggio di registrazione intermedio con **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono previsti tempi di inattività, è possibile usare il sottodominio **cdnverify** di Azure per fornire un passaggio di registrazione intermedio, in modo che gli utenti possano accedere al dominio durante il mapping DNS.  

Dopo aver registrato il dominio personalizzato seguendo una delle procedure descritte in precedenza, è consigliabile [Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [!NOTE]
> Per eseguire il mapping del dominio all'endpoint della rete CDN, è necessario creare un record CNAME con il registrar. I record CNAME eseguono il mapping di sottodomini specifici, ad esempio `www.contoso.com` o `cdn.contoso.com`. Non è possibile eseguire il mapping di un record CNAME a un dominio radice, come `contoso.com`.
> 
> Un sottodominio può essere associato a un solo endpoint della rete CDN. Il record CNAME creato instrada all'endpoint specificato tutto il traffico indirizzato al sottodominio.  Ad esempio, se si associa `www.contoso.com` all'endpoint della rete CDN, non è possibile associarlo ad altri endpoint di Azure, come un endpoint dell'account di archiviazione o del servizio cloud. Tuttavia, è possibile usare più sottodomini dello stesso dominio per endpoint di servizio diversi. È anche possibile eseguire il mapping di sottodomini diversi allo stesso endpoint della rete CDN.
> 
> Per gli endpoint della **rete CDN di Azure fornita da Verizon** (Standard e Premium), la propagazione delle modifiche del dominio personalizzato ai nodi perimetrali della rete CDN può richiedere fino a **90 minuti**.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrare un dominio personalizzato per un endpoint della rete CDN di Azure
1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Fare clic su **Esplora**, quindi su **Profili CDN** e infine sul profilo CDN con l'endpoint di cui si vuole eseguire il mapping a un dominio personalizzato.  
3. Nel pannello **profilo della rete CDN** , fare clic sull'endpoint della rete CDN a cui si desidera associare il sottodominio.
4. Nella parte superiore del pannello dell’endpoint, fare clic sul pulsante **Aggiungi dominio personalizzato** .  Nel pannello **Aggiungi un dominio personalizzato** verrà visualizzato il nome host, derivato dall'endpoint della rete CDN, da usare per la creazione di un nuovo record CNAME. L'indirizzo del nome host viene visualizzato nel formato **&lt;NomeEndpoint>.azureedge.net**.  È possibile copiare questo nome host per usarlo per la creazione del record CNAME.  
5. Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole CNAME, Alias o Subdomains.
7. Creare un nuovo record CNAME per il mapping del sottodominio scelto (ad esempio, **www** o **cdn**) al nome host specificato nel pannello **Aggiungi dominio personalizzato**.
8. Tornare al pannello **Aggiungi un dominio personalizzato** e immettere il dominio personalizzato, incluso il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato `www.contoso.com` o `cdn.contoso.com`.   
   
   Azure verificherà l'esistenza del record CNAME per il nome di dominio immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato.  Per gli endpoint della **Rete CND di Azure fornita da Verizon** (Standard e Premium) possono essere necessari fino a 90 minuti perché le modifiche al dominio personalizzato vengano propagate a tutti nodi perimetrali della rete CDN.  
   
   Notare che in alcuni casi la propagazione del record CNAME nei server dei nomi in Internet può richiedere tempo. Se il dominio non viene convalidato immediatamente e si ritiene corretto il record CNAME, attendere alcuni minuti e quindi riprovare.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrare un dominio personalizzato per un endpoint della rete CDN di Azure usando il sottodominio cdnverify intermedio
1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Fare clic su **Esplora**, quindi su **Profili CDN** e infine sul profilo CDN con l'endpoint di cui si vuole eseguire il mapping a un dominio personalizzato.  
3. Nel pannello **profilo della rete CDN** , fare clic sull'endpoint della rete CDN a cui si desidera associare il sottodominio.
4. Nella parte superiore del pannello dell’endpoint, fare clic sul pulsante **Aggiungi dominio personalizzato** .  Nel pannello **Aggiungi un dominio personalizzato** verrà visualizzato il nome host, derivato dall'endpoint della rete CDN, da usare per la creazione di un nuovo record CNAME. L'indirizzo del nome host viene visualizzato nel formato **&lt;NomeEndpoint>.azureedge.net**.  È possibile copiare questo nome host per usarlo per la creazione del record CNAME.
5. Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.
7. Creare un nuovo record CNAME e specificare un alias di sottodominio che includa il sottodominio **cdnverify** . Ad esempio, il formato del sottodominio specificato sarà **cdnverify.www** o **cdnverify.cdn**. Specificare quindi il nome host, che costituisce l'endpoint della rete CDN, nel formato **cdnverify.&lt;NomeEndpoint>.azureedge.net**.
8. Tornare al pannello **Aggiungi un dominio personalizzato** e immettere il dominio personalizzato, incluso il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato `www.contoso.com` o `cdn.contoso.com`. Notare che in questo passaggio non è necessario anteporre **cdnverify**al sottodominio.  
   
    Azure verificherà l'esistenza del record CNAME per il nome di dominio cdnverify immesso.
9. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non viene ancora instradato all'endpoint della rete CDN. Dopo un'attesa sufficiente per consentire la propagazione delle impostazioni del dominio personalizzato ai nodi perimetrali della rete CDN (90 minuti per la **rete CDN di Azure fornita da Verizon** e 1-2 minuti per la **rete CDN di Azure fornita da Akamai**), tornare al sito Web del registrar DNS e creare un altro record CNAME per il mapping del sottodominio all'endpoint della rete CDN. Ad esempio, specificare il sottodominio come **www** o **cdn** e il nome host come **&lt;NomeEndpoint>.azureedge.net**. Con questo passaggio viene completata la registrazione del dominio personalizzato.
10. Infine, è possibile eliminare il record CNAME creato usando **cdnverify**, in quanto è stato necessario solo come passaggio intermedio.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN
* Una volta completata la registrazione del dominio personalizzato, è possibile accedere al contenuto memorizzato nella cache nell'endpoint della rete CDN usando questo dominio personalizzato.
  Assicurarsi prima di tutto di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN memorizza nella cache il contenuto in contenitori BLOB pubblici. Per testare il dominio personalizzato, assicurarsi che il contenitore sia impostato in modo da consentire l'accesso pubblico e che contenga almeno un BLOB.
* Nel browser passare all'indirizzo del BLOB usando il dominio personalizzato. Se il dominio personalizzato è `cdn.contoso.com`, ad esempio, l'URL di un BLOB memorizzato nella cache sarà simile al seguente: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Vedere anche
[Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure](cdn-create-new-endpoint.md)  




<!--HONumber=Nov16_HO3-->


