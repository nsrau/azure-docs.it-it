---
title: Uso della rete CDN di Azure con firma di accesso condiviso | Documentazione Microsoft
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Uso della rete CDN di Azure con firma di accesso condiviso

Quando si distribuisce contenuto da un contenitore di archiviazione dell'account di archiviazione, è possibile proteggere l'accesso ai file da parte degli utenti concedendo l'accesso privato al contenitore di archiviazione. In caso contrario, se per un contenitore di archiviazione viene concesso l'accesso pubblico, chiunque ne conosca l'URL potrà accedervi. Per proteggere un account di archiviazione al quale la rete per la distribuzione di contenuti (CDN) è stata autorizzata ad accedere, è possibile usare la funzionalità della firma di accesso condiviso in modo da concedere l'accesso limitato ai contenitori di archiviazione privati.

Una firma di accesso condiviso è un URI che concede diritti di accesso limitati alle risorse di Archiviazione di Azure senza esporre la chiave dell'account. È possibile fornire una firma di accesso condiviso ai client non considerati attendibili a cui non si vuole fornire la chiave dell'account di archiviazione ma a ai quali si desidera delegare l'accesso a determinate risorse dell'account di archiviazione. La distribuzione di un URI di firma di accesso condiviso a tali client, consente di concedere loro l'accesso a una risorsa per un periodo di tempo specificato.
 
La firma di accesso condiviso consente di definire vari parametri di accesso a un BLOB, ad esempio l'ora di inizio e di scadenza, le autorizzazioni (di lettura/scrittura) e gli intervalli IP. Questo articolo descrive come usare la firma di accesso condiviso con la rete CDN di Azure. Per altre informazioni sulla firma di accesso condiviso, incluse le procedure per crearla e le opzioni dei parametri, vedere [Uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurazione della rete CDN di Azure per l'uso della firma di accesso condiviso di archiviazione
Di seguito sono riportate le tre opzioni consigliate per l'uso della firma di accesso condiviso con la rete CDN di Azure. Per tutte le opzioni, si presuppone che sia stata già creata una firma di accesso condiviso (vedere Prerequisiti). 
 
### <a name="prerequisites"></a>prerequisiti
Per iniziare, creare un account di archiviazione e quindi generare una firma di accesso condiviso per l'asset. È possibile generare due tipi di firme di accesso condiviso: una firma di accesso condiviso del servizio o una firma di accesso condiviso dell'account. Per altre informazioni, vedere [Tipi di firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Dopo aver generato una firma di accesso condiviso, è possibile accedere al file di archiviazione BLOB mediante un URL con il formato seguente:  `https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Ad esempio: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Per altre informazioni sull'impostazione dei parametri, vedere [Considerazioni sui parametri della firma di accesso condiviso](#sas-parameter-considerations) e [Parametri della firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Impostazioni della firma di accesso condiviso con la rete CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>Opzione 1: Uso della firma di accesso condiviso con pass-through all'archiviazione BLOB dalla rete CDN

È l'opzione è più semplice e prevede solo l'uso di un singolo token di firma di accesso condiviso, che viene passato dalla rete CDN al server di origine. È supportata dalla **rete CDN di Azure fornita da Verizon**, sia per i profili Standard e che Premium, e dalla **rete CDN di Azure fornita da Akamai**. 
 
1. Selezionare un endpoint, fare clic su **Regole di memorizzazione nella cache**, quindi selezionare **Memorizza nella cache tutti gli URL univoci** dall'elenco **Comportamento di memorizzazione nella cache della stringa di query**.

    ![Regole di memorizzazione nella cache della rete CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Dopo aver configurato la firma di accesso condiviso nell'account di archiviazione, usare il token di firma di accesso condiviso con l'URL della rete CDN per accedere al file. 
   
   L'URL di firma di accesso condiviso risultante ha il formato seguente: `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Ad esempio:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Specificare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo intestazioni `Cache-Control` all'origine. Poiché la rete CDN considera il token di firma di accesso condiviso come una stringa di query semplice, la procedura consigliata prevede la configurazione di una durata della cache con scadenza alla stessa ora di scadenza della firma di acceso condiviso o a un'ora antecedente. In caso contrario, se un file viene memorizzato nella cache per un periodo più lungo di quello in cui la firma di accesso condiviso è attiva, una volta scaduta la firma di accesso condiviso, sarà possibile accedere a tale file dal server di origine della rete CDN. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di ripulitura sul file per cancellarlo dalla cache. Per informazioni sull'impostazione della durata della cache sulla rete CDN, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>Opzione 2: Token di sicurezza della rete CDN nascosto tramite una regola di riscrittura
 
Con questa opzione è possibile proteggere l'archiviazione BLOB di origine senza richiedere un token di firma di accesso condiviso per l'utente della rete CDN. È consigliabile usarla se non sono necessarie restrizioni di accesso specifiche per il file, ma si desidera impedire agli utenti di accedere all'origine di archiviazione in maniera diretta per migliorare i tempi di offload della rete CDN. Questa opzione è disponibile solo per i profili di **rete CDN Premium di Azure fornita da Verizon**. 
 
1. Usare il [motore regole](cdn-rules-engine.md) per creare una regola di riscrittura URL. La propagazione delle nuove regole richiede circa 90 minuti.

   ![Pulsante Gestisci della rete CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Pulsante Motore regole della rete CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Questa regola di riscrittura URL di esempio contiene i criteri seguenti:
   
   Origine:   
   `/test/demo.jpg`
   
   Destinazione:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regola di riscrittura URL della rete CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Accedere al file sulla rete CDN senza il token di firma di accesso condiviso, con il formato seguente: `https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Ad esempio:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Si noti che chiunque, indipendentemente dal fatto che usi un token di firma di accesso condiviso o meno, può accedere a un endpoint della rete CDN. 

3. Specificare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo intestazioni `Cache-Control` all'origine. Poiché la rete CDN considera il token di firma di accesso condiviso come una stringa di query semplice, la procedura consigliata prevede la configurazione di una durata della cache con scadenza alla stessa ora di scadenza della firma di acceso condiviso o a un'ora antecedente. In caso contrario, se un file viene memorizzato nella cache per un periodo più lungo di quello in cui la firma di accesso condiviso è attiva, una volta scaduta la firma di accesso condiviso, sarà possibile accedere a tale file dal server di origine della rete CDN. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di ripulitura sul file per cancellarlo dalla cache. Per informazioni sull'impostazione della durata della cache sulla rete CDN, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opzione 3: Uso dell'autenticazione tramite token di sicurezza della rete CDN con una regola di riscrittura

Questa opzione è la più sicura e personalizzabile. Per usare l'autenticazione tramite token di sicurezza della rete CDN, è necessario disporre di un profilo di **rete CDN Premium di Azure fornita da Verizon**. L'accesso del client si basa sui parametri di sicurezza impostati nel token di sicurezza della rete CDN. Tuttavia, se la firma di accesso condiviso diventa non valida, la rete CDN non sarà in grado di riconvalidare il contenuto del server di origine.

1. [Creare un token di sicurezza della rete CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) e attivarlo tramite il motore regole per l'endpoint della rete CDN e il percorso in cui gli utenti possono accedere al file.

   Un URL di firma di accesso condiviso ha il seguente formato:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Ad esempio:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Le opzioni dei parametri per l'autenticazione tramite token di sicurezza della rete CDN sono diverse rispetto quelle per un token di firma di accesso condiviso. Se si sceglie di usare un scadenza quando si crea un token di sicurezza della rete CDN, impostarla sullo stesso valore di scadenza del token di firma di accesso condiviso, in modo da garantire che sia stimabile. 
 
2. Usare il [motore regole](cdn-rules-engine.md) per creare una regola di riscrittura URL per abilitare l'accesso tramite token a tutti i BLOB presenti nel contenitore. La propagazione delle nuove regole richiede circa 90 minuti.

   Questa regola di riscrittura URL di esempio contiene i criteri seguenti:
   
   Origine:   
   `/test/demo.jpg`
   
   Destinazione:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regola di riscrittura URL della rete CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. Quando si rinnova la firma di accesso condiviso, aggiornare la regola di riscrittura URL per usare il nuovo token di firma di accesso condiviso. 

## <a name="sas-parameter-considerations"></a>Considerazioni sui parametri della firma di accesso condiviso

Poiché i parametri della firma di accesso condiviso non sono visibili alla rete CDN, quest'ultima non può modificare il comportamento di distribuzione in base ad essi. Le restrizioni dei parametri definiti si applicano solo alle richieste inviate dalla rete CDN al server di origine e non alle richieste inviate dal client alla rete CDN. È importante tenere presente questa distinzione quando si impostano i parametri della firma di accesso condiviso. Se queste funzionalità avanzate sono necessarie e si usa l'[opzione 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), impostare le restrizioni appropriate nel token di sicurezza della rete CDN.

| Nome del parametro della firma di accesso condiviso | DESCRIZIONE |
| --- | --- |
| Inizia | Ora in cui la rete CDN può iniziare ad accedere al file di BLOB. A causa di uno sfasamento del clock (quando un segnale di clock perviene in momenti diversi per i diversi componenti), scegliere un'ora con 15 di anticipo se si vuole che l'asset sia immediatamente disponibile. |
| End | Ora dopo la quale la rete CDN non può più accedere al file di BLOB. I file precedentemente memorizzati nella cache sulla rete CDN sono ancora accessibili. Per controllare l'ora di scadenza dei file, impostare l'ora di scadenza appropriata nel token di sicurezza della rete CDN o ripulire l'asset. |
| Indirizzi IP consentiti | facoltativo. Se si usa la **rete CDN di Azure fornita da Verizon**, è possibile impostare questo parametro sugli intervalli definiti in [Rete CDN di Azure da intervalli di indirizzi IP per server perimetrali Verizon](https://msdn.microsoft.com/library/mt757330.aspx). Se si usa la **rete CDN di Azure fornita da Akamai**, non è possibile impostare il parametro degli intervalli IP poiché gli indirizzi IP non sono statici.|
| Protocolli consentiti | Uno o più protocolli consentiti per una richiesta effettuata con la firma di acceso condiviso dell'account. È consigliabile usare l'impostazione HTTPS.|

## <a name="see-also"></a>Vedere anche 
- [Uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Protezione di asset della rete per la distribuzione di contenuti (CDN) di Azure con l'autenticazione basata su token](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
