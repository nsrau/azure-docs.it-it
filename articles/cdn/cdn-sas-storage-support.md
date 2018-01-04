---
title: Utilizzo rete CDN di Azure con firma di accesso condiviso | Documenti Microsoft
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Utilizzo rete CDN di Azure con firma di accesso condiviso

Quando si fornisce contenuto dal contenitore di archiviazione dell'account di archiviazione, si desidera proteggere come gli utenti possono accedere i file tramite la concessione di accesso private per il contenitore di archiviazione. In caso contrario, un contenitore di archiviazione per cui è stato concesso l'accesso pubblico accessibile da qualsiasi utente che conosce l'URL. Per proteggere un account di archiviazione che è la rete di distribuzione di contenuti (CDN) di accedere, è possibile utilizzare la funzionalità di firma di accesso condiviso (SAS) dall'archiviazione di Azure per concedere l'accesso limitato ai contenitori di archiviazione privato.

Una firma di accesso condiviso è un URI che concede i diritti di accesso alle risorse di archiviazione di Azure limitati senza esporre la chiave dell'account. È possibile fornire una firma di accesso condiviso ai client non attendibili con la chiave di account di archiviazione, ma a cui si desidera delegare l'accesso a determinate risorse di account di archiviazione. Distribuendo una firma di accesso condiviso URI per questi client, si concedergli l'accesso a una risorsa per un periodo di tempo specificato.
 
Firma di accesso condiviso consente di definire i vari parametri di accesso a un blob, ad esempio l'ora di inizio e di scadenza, le autorizzazioni (lettura/scrittura) e gli intervalli di IP. In questo articolo viene descritto come utilizzare SAS in combinazione con la rete CDN Azure. Per ulteriori informazioni sulle firme, incluse informazioni sulla creazione e le opzioni di parametro, vedere [Using condiviso (SAS) le firme di accesso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Impostazione rete CDN di Azure per lavorare con l'archiviazione SAS
Le tre opzioni seguenti sono consigliate per l'utilizzo di firma di accesso condiviso con la rete CDN Azure. Tutte le opzioni, si supponga che un firma di accesso condiviso (vedere la sezione Prerequisiti) di lavoro è già stato creato. 
 
### <a name="prerequisites"></a>Prerequisiti
Per iniziare, creare un account di archiviazione e quindi generare una firma di accesso condiviso per l'asset. È possibile generare due tipi di firme di accesso archiviati: un servizio di firma di accesso condiviso o un account di firma di accesso condiviso. Per ulteriori informazioni, vedere [tipi di firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Dopo aver generato una firma di accesso condiviso, è possibile accedere a file di archiviazione blob con un URL che ha il formato seguente:`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Ad esempio: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Per ulteriori informazioni sull'impostazione dei parametri, vedere [considerazioni sui parametri SAS](#sas-parameter-considerations) e [parametri di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Impostazioni di CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>Opzione 1: Utilizzo di firma di accesso condiviso con pass-through nel blob di archiviazione dalla rete CDN

Questa opzione è la più semplice e viene utilizzato solo un token SAS singolo, che viene passato al server di origine dalla rete CDN. È supportato da **rete CDN di Azure da Verizon**, per i profili di Standard e Premium, e **rete CDN di Azure da Akamai**. 
 
1. Selezionare un endpoint, fare clic su **la memorizzazione nella cache regole**, quindi selezionare **memorizzare nella Cache tutti gli URL univoci** dal **memorizzazione nella cache di stringa di Query** elenco.

    ![Regole di memorizzazione nella cache della rete CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Dopo aver impostato SAS sull'account di archiviazione, usare il token di firma di accesso condiviso con l'URL della rete CDN per accedere al file. 
   
   L'URL risulta ha il formato seguente:`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Ad esempio:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ottimizzare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo `Cache-Control` intestazioni in corrispondenza dell'origine. Poiché la rete CDN considera il token di firma di accesso condiviso come una stringa di query semplice, come procedura consigliata è necessario impostare una durata di memorizzazione nella cache scade in corrispondenza o prima della scadenza firma di accesso condiviso. In caso contrario, se un file è memorizzato nella cache per un periodo più lungo di firma di accesso condiviso è attivo, il file sia accessibile dal server di origine della rete CDN dopo che è trascorso il tempo di scadenza della firma di accesso condiviso. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di eliminazione sul file per cancellarla dalla cache. Per informazioni sull'impostazione la durata della cache per la rete CDN, vedere [controllo Azure Content Delivery Network comportamento di memorizzazione nella cache con la memorizzazione nella cache regole](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>Opzione 2: Nascosto mediante una regola di riscrittura token di sicurezza di rete CDN
 
Con questa opzione, è possibile proteggere lo spazio di archiviazione blob di origine senza richiedere un token di firma di accesso condiviso per l'utente della rete CDN. È consigliabile utilizzare questa opzione se non è necessario restrizioni di accesso specifici per il file, ma desidera impedire agli utenti di accedere all'origine di archiviazione direttamente per migliorare i tempi di offload della rete CDN. Questa opzione è disponibile solo per **Premium rete CDN di Azure da Verizon** profili. 
 
1. Utilizzare il [motore regole di business](cdn-rules-engine.md) per creare una regola di riscrittura dell'URL. Nuove regole necessari circa 90 minuti per propagare.

   ![Pulsante Gestisci rete CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Pulsante di motore regole di rete CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Questa regola di riscrittura dell'URL di esempio con i modelli seguenti:
   
   Origine:   
   `/test/demo.jpg`
   
   Destinazione:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regola di riscrittura dell'URL della rete CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Accedere al file della rete CDN senza il token di firma di accesso condiviso, nel formato seguente:`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Ad esempio:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Si noti che chiunque, indipendentemente dal fatto che utilizzano un token di firma di accesso condiviso, può accedere a un endpoint rete CDN. 

3. Ottimizzare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo `Cache-Control` intestazioni in corrispondenza dell'origine. Poiché la rete CDN considera il token di firma di accesso condiviso come una stringa di query semplice, come procedura consigliata è necessario impostare una durata di memorizzazione nella cache scade in corrispondenza o prima della scadenza firma di accesso condiviso. In caso contrario, se un file è memorizzato nella cache per un periodo più lungo di firma di accesso condiviso è attivo, il file sia accessibile dal server di origine della rete CDN dopo che è trascorso il tempo di scadenza della firma di accesso condiviso. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di eliminazione sul file per cancellarla dalla cache. Per informazioni sull'impostazione la durata della cache per la rete CDN, vedere [controllo Azure Content Delivery Network comportamento di memorizzazione nella cache con la memorizzazione nella cache regole](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opzione 3: Una regola di riscrittura con l'autenticazione basata su token di sicurezza della rete CDN

Questa opzione è la più sicura e personalizzabile. Per utilizzare l'autenticazione basata su token di sicurezza della rete CDN, è necessario un **Premium rete CDN di Azure da Verizon** profilo. Accesso client è in base ai parametri di sicurezza impostata sul token di sicurezza della rete CDN. Tuttavia, se la firma di accesso condiviso diventa non valido, la rete CDN sarà in grado di convalidare il contenuto dal server di origine.

1. [Creare un token di sicurezza della rete CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e attivarlo usando il motore di regole per l'endpoint rete CDN e il percorso in cui gli utenti possono accedere al file.

   Un URL di firma di accesso condiviso ha il seguente formato:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Ad esempio:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Le opzioni di parametro per l'autenticazione token di sicurezza della rete CDN sono diverse rispetto alle opzioni di parametro per un token di firma di accesso condiviso. Se si sceglie di utilizzare un'ora di scadenza quando si crea un token di sicurezza della rete CDN, impostarla per lo stesso valore di ora di scadenza per il token di firma di accesso condiviso. In modo da garantire che l'ora di scadenza è stimabile. 
 
2. Utilizzare il [motore regole di business](cdn-rules-engine.md) per creare una regola di riscrittura dell'URL per abilitare l'accesso token per tutti i BLOB nel contenitore. Nuove regole necessari circa 90 minuti per propagare.

   Questa regola di riscrittura dell'URL di esempio con i modelli seguenti:
   
   Origine:   
   `/test/demo.jpg`
   
   Destinazione:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regola di riscrittura dell'URL della rete CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. Quando si rinnova la firma di accesso condiviso, è possibile aggiornare la regola di riscrittura dell'Url per utilizzare il nuovo token di firma di accesso condiviso. 

## <a name="sas-parameter-considerations"></a>Considerazioni sui parametri di firma di accesso condiviso

Poiché i parametri SAS non sono visibili nella rete CDN, la rete CDN non è possibile modificare il comportamento di distribuzione basato su di essi. Le restrizioni di parametro definito si applicano solo alle richieste che la rete CDN consente al server di origine, non per le richieste dal client alla rete CDN. Questa distinzione è importante prendere in considerazione quando si impostano i parametri SAS. Se queste funzionalità avanzate sono necessari e si utilizza [opzione 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), impostare le restrizioni appropriate per il token di sicurezza della rete CDN.

| Nome del parametro SAS | DESCRIZIONE |
| --- | --- |
| Inizia | Ora in cui è possibile iniziare la rete CDN per accedere al file di blob. A causa di orologio inclinare (quando un segnale di clock arriva in momenti diversi per i diversi componenti), scegliere un tempo di 15 minuti precedenti se si desidera che l'asset sia immediatamente disponibile. |
| End | Il tempo dopo il quale la rete CDN non è più possibile accedere al file di blob. In precedenza i file memorizzati nella cache nella rete CDN sono ancora accessibili. Per controllare l'ora di scadenza di file, impostare l'ora di scadenza appropriata nel token di sicurezza della rete CDN o eliminare l'asset. |
| Indirizzi IP consentiti | facoltativo. Se si utilizza **rete CDN di Azure da Verizon**, è possibile impostare questo parametro per gli intervalli definiti [rete CDN di Azure da intervalli di IP Server Edge Verizon](https://msdn.microsoft.com/library/mt757330.aspx). Se si utilizza **rete CDN di Azure da Akamai**, poiché gli indirizzi IP non sono statici, non è possibile impostare il parametro di intervalli IP.|
| Protocolli consentiti | Protocolli consentiti per una richiesta effettuata con l'account sa. È consigliabile l'impostazione HTTPS.|

## <a name="see-also"></a>Vedere anche 
- [Uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Protezione delle risorse di rete CDN Azure con l'autenticazione token](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
