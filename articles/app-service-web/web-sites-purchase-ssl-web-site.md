---
title: Aggiungere un certificato SSL all&quot;app del Servizio app di Azure | Documentazione Microsoft
description: Informazioni su come aggiungere un certificato SSL all&quot;app del Servizio app.
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>Aggiungere un certificato SSL all'app del Servizio app
> [!div class="op_single_selector"]
> * [Acquistare un certificato SSL in Azure](web-sites-purchase-ssl-web-site.md)
> * [Usare un certificato SSL proveniente da un'altra posizione](web-sites-configure-ssl-certificate.md)
> 
> 

Per impostazione predefinita, il [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) abilita già HTTPS per un'app Web con un certificato dotato di caratteri jolly per il dominio \*.azurewebsites.net. Se non si intende impostare un dominio personalizzato, è possibile usare il certificato HTTPS predefinito. Tuttavia, come tutti i [domini con caratteri jolly](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), il certificato con caratteri jolly di Azure non è sicuro quanto un dominio personalizzato con il proprio certificato.

Il Servizio app offre un metodo semplificato per acquistare e gestire un certificato SSL nel portale di Azure. 

Questo articolo spiega come acquistare e impostare un certificato SSL nell'app [Servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). 

> [!NOTE]
> È possibile usare i certificati SSL per i nomi di dominio personalizzato per le app ospitate in un piano di servizio app Condiviso o Gratuito. Per usare i certificati SSL, l'app Web deve essere ospitata in un piano di servizio Basic, Standard o Premium. La modifica del tipo di sottoscrizione potrebbe cambiare la modalità di addebito per la stessa. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/web-sites/).
> 
> 

> [!WARNING]
> Non tentare l'acquisto di un certificato SSL tramite l'uso di una sottoscrizione per cui non vi è un collegamento a una carta di credito valida. Ciò potrebbe causare la disabilitazione della sottoscrizione. 
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per abilitare HTTPS per un dominio personalizzato, iniziare con il [mapping di un nome di dominio personalizzato nell'app di Azure](web-sites-custom-domain-name.md).

Prima di richiedere un certificato SSL, determinare i nomi di dominio da proteggere con il certificato. Ciò determina il tipo di certificato necessario. Se si intende proteggere un singolo nome di dominio, come contoso.com *o* www.contoso.com, sarà possibile usare un certificato Standard (di base). Se è necessario proteggere più nomi di dominio, come contoso.com, www.contoso.com *e* mail.contoso.com, è possibile ottenere un [certificato con caratteri jolly](http://en.wikipedia.org/wiki/Wildcard_certificate).

## <a name="bkmk_purchasecert"></a>Acquistare un certificato SSL

1. Nel [Portale di Azure](https://portal.azure.com/), nel menu, selezionare **Sfoglia**. Nella casella **Cerca** digitare **Certificato del servizio app**. Trai risultati della ricerca, selezionare **Certificati del servizio app**. 

   ![Creazione tramite il pulsante Sfoglia](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. Nella pagina **Certificati del servizio app** selezionare **Aggiungi**. 

   ![Aggiungere un certificato](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. Immettere un **nome** per il certificato SSL.
4. Immettere il **nome host**.
   
   > [!WARNING]
   > Questa è una delle parti più importanti del processo di acquisto. Assicurarsi di immettere il nome host corretto (nome di dominio personalizzato) che si desidera venga protetto da questo certificato. *Non* aggiungere "www" all'inizio del nome host. Ad esempio, se il nome di dominio personalizzato è www.contoso.com, immettere **contoso.com** nella casella **Nome host**. Il certificato proteggerà www e i domini radice. 
   > 

5. Selezionare la propria **sottoscrizione**. 
   
   Se si dispone di più sottoscrizioni, creare un certificato SSL nella stessa sottoscrizione che si usa per il dominio personalizzato o per l'app Web.

6. Selezionare o creare un **gruppo di risorse**.
   
   È possibile usare gruppi di risorse per gestire insieme le risorse di Azure correlate. I gruppi di risorse sono utili quando si desidera stabilire delle regole di controllo degli accessi in base al ruolo (RBAC) per le app. Per altre informazioni, vedere Gestione delle risorse di Azure.

7. Selezionare lo **SKU del certificato**. 
   
   Selezionare lo SKU del certificato adatto alle proprie esigenze e quindi selezionare **Crea**. 
   
   È possibile scegliere tra due SKU nel Servizio app:
   * **S1**: certificato Standard con validità di un anno e rinnovo automatico  
   * **W1**: certificato con caratteri jolly con validità di un anno e rinnovo automatico       
  
    ![Certificato SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> La creazione di un certificato SSL può richiedere fino a 10 minuti. Il processo prevede più passaggi che vengono eseguiti in background.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>Archiviare il certificato in Azure Key Vault

1. Dopo aver completato l'acquisto di certificati SSL, nel portale di Azure passare al pannello **Certificati del servizio app**.

   ![Certificato pronto per l'archiviazione in Key Vault](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Si noti che lo stato del certificato è **In attesa di rilascio**. È necessario completare alcuni passaggi prima di poter iniziare a usare questo certificato.

2. Nel pannello delle **Proprietà certificato** selezionare **Configurazione certificati**. Per archiviare questo certificato nell'insieme di credenziali chiave, selezionare **Passaggio 1: archiviare**.
3. Nel pannello **Stato insieme di credenziali delle chiavi** selezionare un insieme di credenziali chiave esistente per archiviare questo certificato, quindi selezionare **Repository dell'insieme di credenziali delle chiavi**.  Per creare un nuovo insieme di credenziali chiave nella stessa sottoscrizione e nel gruppo di risorse, selezionare **Crea nuovo insieme di credenziali delle chiavi**.

   ![Crea nuovo insieme di credenziali delle chiavi](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > Azure Key Vault prevede addebiti minimi per l'archiviazione di questo certificato. Per altre informazioni, vedere [Prezzi di Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
   > 
   > 

4. Dopo aver selezionato l'archivio dell'insieme di credenziali delle chiavi in cui archiviare il certificato, nella parte superiore del pannello **Stato insieme di credenziali delle chiavi** selezionare il pulsante **Archivia**.  
   
Per verificare la selezione, è possibile fare clic sul pulsante di aggiornamento del browser. Un segno di spunta verde mostra che questo passaggio è stato completato.

## <a name="bkmk_VerifyOwnership"></a>Verificare la proprietà del dominio

1. Nel pannello **Configurazione certificati** selezionare **Passaggio 2: verificare**.
2. Selezionare le opzioni di verifica usando le informazioni seguenti. 

I certificati del Servizio app supportano tre tipi di verifica del dominio:

   * Verifica del dominio
   * Verifica tramite posta elettronica
   * Verifica manuale

### <a name="domain-verification"></a>Verifica del dominio 
     
La verifica del dominio è il processo più semplice *solo* in caso di [acquisto del dominio personalizzato dal Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md).

1. Per completare questo passaggio, selezionare **Verifica**.
2. Per aggiornare lo stato del certificato dopo aver completato la verifica, fare clic su **Aggiorna**. Il completamento della verifica potrebbe richiedere qualche minuto.

### <a name="mail-verification"></a>Verifica tramite posta elettronica
     
Con un dominio personalizzato, un messaggio di posta elettronica di verifica è stato inviato agli indirizzi di posta elettronica associati al dominio personalizzato. 

1. Per completare il passaggio di verifica tramite posta elettronica, aprire la posta elettronica e fare clic sul collegamento di verifica. 
2. Se è necessario un nuovo invio del messaggio di verifica, fare clic sul pulsante **Invia di nuovo il messaggio di posta elettronica**.

### <a name="manual-verification"></a>Verifica manuale    
     
**Verifica della pagina Web HTML (funziona solo con lo SKU del certificato Standard)**

1. Creare un file HTML denominato starfield.html. Il contenuto di questo file deve corrispondere al nome preciso del token di verifica del dominio. È possibile copiare il token dal pannello **Domain Verification Status** (Stato di verifica del dominio).
2. Caricare questo file nella radice del server Web in cui si ospita il dominio. Ad esempio, /.well-known/pki-validation/starfield.html.
3.  Una volta conclusa la verifica, per aggiornare lo stato del certificato, selezionare **Aggiorna**. Il completamento della verifica potrebbe richiedere qualche minuto.

    Se si acquista ad esempio un certificato standard per **contosocertdemo.com** con token di verifica del dominio **tgjgthq8d11ttaeah97s3fr2sh**, una richiesta Web a **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** dovrà restituire **tgjgthq8d11ttaeah97s3fr2sh**.

**Verifica del record TXT DNS**
        
1. Usando il gestore DNS, creare un record TXT nel sottodominio **@** con un valore uguale al **token di verifica del dominio**.
2. Per aggiornare lo stato delle certificazioni una volta conclusa la verifica, selezionare **Aggiorna**. Il completamento della verifica potrebbe richiedere qualche minuto.
 
   Ad esempio, per eseguire la convalida di un certificato con caratteri jolly con nome host **\*.contosocertdemo.com** o **\*.subdomain.contosocertdemo.com** e token di verifica del dominio **tgjgthq8d11ttaeah97s3fr2sh**, creare un record TXT in **contosocertdemo.com** con valore **tgjgthq8d11ttaeah97s3fr2sh**.     

## <a name="bkmk_AssignCertificate"></a>Assegnare il certificato a un'app del Servizio app

> [!NOTE]
> Prima di completare la procedura inclusa in questa sezione, è necessario associare un nome di dominio personalizzato all'app. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per un'app Web](web-sites-custom-domain-name.md).
> 
> 

1. Nel [Portale di Azure](https://portal.azure.com/), nel menu, selezionare **Servizio app**.
2. Selezionare il nome dell'app a cui si desidera assegnare questo certificato. 
3. Passare a **Impostazioni** > **Certificati SSL** > **Importa il certificato del servizio app** e quindi selezionare il certificato.

   ![Importare il certificato](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. Nella sezione **Associazioni SSL** selezionare **Add bindings** (Aggiungi associazioni).
5. Nel pannello **Aggiungi associazione SSL** selezionare il nome di dominio che si desidera proteggere con il certificato SSL. Selezionare il certificato da usare. È possibile anche stabilire se usare il metodo SSL basato su [Indicazione nome server (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) o su IP.

   ![Associazioni SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * Per associare un certificato a un nome di dominio, gli SSL basati su IP eseguono il mapping dell'indirizzo IP pubblico dedicato del server al nome di dominio. Quando si usa un SSL basato su IP, ogni nome di dominio (ad esempio, contoso.com o fabricam.com) associato al servizio deve avere un indirizzo IP dedicato. Questo è il metodo tradizionale per l'associazione di un certificato SSL a un server Web.
    * SSL basato su SNI è un'estensione di SSL e [Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS). Quando si usa SSL basato su SNI, più domini possono condividere lo stesso indirizzo IP. Ogni dominio dispone di un certificato di sicurezza separato. La maggior parte dei browser moderni, inclusi Internet Explorer, Chrome, Firefox e Opera, supporta SNI. I browser meno recenti potrebbero non supportare SNI. Per altre informazioni su SNI, vedere [Indicazione nome server](http://en.wikipedia.org/wiki/Server_Name_Indication) su Wikipedia.

6. Selezionare **Aggiungi l'associazione** per salvare le modifiche e abilitare SSL.

Se si seleziona l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario completare i passaggi aggiuntivi seguenti.

1.  Dopo l'impostazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato all'app. Per trovare l'indirizzo IP, passare a **Impostazioni** > **Dominio personalizzato**. Immediatamente sopra la sezione **Nomi host**, l'indirizzo IP è elencato come **Indirizzo IP esterno**.

   ![SSL basato su IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  Si noti che questo indirizzo IP è diverso rispetto all'indirizzo IP virtuale usato in precedenza per la configurazione del record A per il dominio. Se l'app è configurata per usare SSL basato su SNI o se non è configurata per usare SSL, non viene elencato alcun IP.

2.  Usando gli strumenti forniti dal registrar del nome di dominio, modificare il record A per il nome di dominio personalizzato, in modo che faccia riferimento all'indirizzo IP riportato nel passaggio precedente.

3.  Per verificare che il certificato sia stato configurato correttamente, passare all'app usando HTTPS:// invece di HTTP://.

## <a name="bkmk_Export"></a>Esportare il certificato del servizio app
È possibile creare una copia PFX locale di un certificato del Servizio app. Quando si dispone di una copia locale, è possibile usare il certificato con altri servizi di Azure. Per altre informazioni, vedere il post di blog [Create a local PFX copy of your App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/) (Creare una copia locale PFX del certificato del Servizio app).

## <a name="bkmk_Renew"></a>Rinnovo automatico del certificato del servizio app
Per configurare le impostazioni di rinnovo automatico del certificato o per rinnovare manualmente il certificato, nel pannello **Proprietà certificato** selezionare **Impostazioni di rinnovo automatico**. 

![Impostazioni di rinnovo automatico](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

Per rinnovare automaticamente il certificato prima della scadenza, impostare **Rinnovo automatico** su **ON**. Questa è l'opzione predefinita. Se è attivato il rinnovo automatico, si tenta di rinnovare il certificato a partire dal 90° giorno prima della scadenza del certificato. Se sono state create associazioni SSL sulle app del servizio app nel portale di Azure, anche le associazioni sono aggiornate quando il nuovo certificato è pronto (ad esempio, in scenari di sincronizzazione e di reimpostazione delle chiavi). 

Se si desidera gestire manualmente i rinnovi, impostare **Rinnovo automatico** su **Off**. È possibile rinnovare manualmente un certificato del servizio app solo quando la data di scadenza è entro 90 giorni.

## <a name="bkmk_Rekey"></a>Reimpostare le chiavi e sincronizzare il certificato

1. Se si devono reimpostare le chiavi del certificato (per motivi di sicurezza), nel pannello **Proprietà certificato** selezionare **Reimposta chiavi e sincronizza**. 
2. Selezionare **Reimposta chiavi**. Il completamento del processo potrebbe richiedere fino a 10 minuti. 

   ![Reimpostare le chiavi SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

Ecco alcune informazioni aggiuntive sulla reimpostazione delle chiavi:

* La reimpostazione delle chiavi del certificato distribuisce al certificato un nuovo certificato. Il nuovo certificato viene rilasciato dall'autorità di certificazione.
* Non sono previsti addebiti per la reimpostazione delle chiavi per l'intera durata del certificato. 
* La reimpostazione delle chiavi del certificato garantisce un stato di **In attesa di rilascio**. 
* Quando il certificato è pronto, per evitare l'interruzione del servizio, assicurarsi di sincronizzare le risorse usando il certificato.
* Non è disponibile l'opzione di sincronizzazione per i certificati non ancora assegnati a un'app Web. 

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere il dominio personalizzato dell'app tramite HTTPS](web-sites-configure-ssl-certificate.md)
* [Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md)
* [Centro protezione Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](https://azure.microsoft.com/try/app-service/). È possibile creare un'app Web iniziale temporanea nel servizio app. Non è richiesta una carta di credito, né di impegnarsi in alcun modo.
> 
> 

