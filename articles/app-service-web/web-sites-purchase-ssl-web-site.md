---
title: Acquistare e configurare un certificato SSL per il servizio app di Azure
description: Informazioni su come acquistare e configurare un certificato SSL per il servizio app di Azure.
services: app-service
documentationcenter: .net
author: apurvajo
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
ms.author: apurvajo
translationtype: Human Translation
ms.sourcegitcommit: a1b492b7884deb2d0d4f255af0737e1633606384
ms.openlocfilehash: 0a016d88b8d7a800bf726e4f582deeaaf3bc6ad6


---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Acquistare e configurare un certificato SSL per il servizio app di Azure
> [!div class="op_single_selector"]
> * [Acquistare un certificato SSL in Azure](web-sites-purchase-ssl-web-site.md)
> * [Usare un certificato SSL per un dominio personalizzato](web-sites-configure-ssl-certificate.md)
> 
> 

Per impostazione predefinita, il **[servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** abilita già HTTPS per un'app Web con un certificato con caratteri jolly per il dominio *.azurewebsites.net. Se non si intende configurare un dominio personalizzato, è possibile usare il certificato HTTPS predefinito. Come tutti i *[domini con caratteri jolly](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), tuttavia, non è sicuro quanto un dominio personalizzato con il proprio certificato. Il servizio app di Azure offre ora un modo molto semplice per acquistare e gestire un certificato SSL direttamente dal portale di Azure, senza mai uscire dal portale stesso.  
Questo articolo spiega come acquistare e configurare un certificato SSL per il **[Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** in 3 semplici passaggi. 

> [!NOTE]
> Non è possibile usare certificati SSL per i nomi di dominio personalizzati con un'app Web inclusa nel piano Gratuito e Condiviso. È necessario configurare l'app Web per la modalità Basic, Standard o Premium. Gli importi fatturati per la sottoscrizione variano a seconda della modalità scelta. Per altre informazioni vedere **[Prezzi di Servizio app](https://azure.microsoft.com/pricing/details/web-sites/)**.
> 
> 

## <a name="a-namebkmkoverviewaoverview"></a><a name="bkmk_Overview"></a>Panoramica
> [!NOTE]
> Non tentare di acquistare un certificato SSL con una sottoscrizione a cui non è associata una carta di credito attiva, perché è possibile che la sottoscrizione venga disabilitata. 
> 
> 

## <a name="a-purchase-store-and-assign-an-ssl-certificate-for-your-custom-domain-a"></a><a> Acquistare, archiviare e assegnare un certificato SSL per il dominio personalizzato </a>
Per abilitare HTTPS per un dominio personalizzato, ad esempio contoso.com, è necessario prima **[configurare un nome di dominio personalizzato nel Servizio app di Azure.](web-sites-custom-domain-name.md)**

Prima di richiedere un certificato SSL, è necessario determinare i nomi di dominio da proteggere tramite il certificato, in modo da stabilire il tipo di certificato che è necessario ottenere. Se si vuole proteggere solo un singolo nome di dominio, ad esempio contoso.com or www.contoso.com, sarà sufficiente un certificato Standard (di base). Se è necessario proteggere più nomi di dominio, ad esempio contoso.com, www.contoso.com e mail.contoso.com, è possibile ottenere un **[certificato con caratteri jolly](http://en.wikipedia.org/wiki/Wildcard_certificate)**

## <a name="a-namebkmkpurchasecertastep-0-place-an-ssl-certificate-order"></a><a name="bkmk_purchasecert"></a>Passaggio 0: eseguire un ordine per un certificato SSL
Questo passaggio descrive come eseguire un ordine per il certificato SSL desiderato.

1. Nel **[Portale di Azure](https://portal.azure.com/)** fare clic su Sfoglia e digitare "Certificati del servizio app" nella barra di ricerca, quindi selezionare "Certificati del servizio app" dai risultati e fare clic su Aggiungi. 
   
   ![inserimento immagine della creazione con il pulsante Sfoglia](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
   ![inserimento immagine della creazione con il pulsante Sfoglia](./media/app-service-web-purchase-ssl-web-site/add.jpg)
2. Immettere un **nome descrittivo** per il certificato SSL.
3. Immettere il **nome host**
   
   > [!NOTE]
   > Questa è una delle parti più importanti del processo di acquisto. Assicurarsi di immettere il nome host corretto (dominio personalizzato) che si desidera proteggere con il certificato. **NON** aggiungere WWW al nome host. Ad esempio, se il nome di dominio personalizzato è www.contoso.com, sarà sufficiente immettere contoso.com nel campo Nome host e il certificato in questione proteggerà sia domini www sia domini radice. 
   > 
   > 
4. Selezionare la propria **sottoscrizione**. 
   
   Se si dispone di più sottoscrizioni, verificare di creare un certificato SSL nella stessa sottoscrizione usata per il dominio personalizzato o per l'app Web in questione.
5. Selezionare o creare un **gruppo di risorse**.
   
   I gruppi di risorse consentono di gestire risorse di Azure correlate come un'unità e sono utili per stabilire le regole di controllo degli accessi in base al ruolo (RBAC) per le app. Per altre informazioni, vedere Gestione delle risorse di Azure.
6. Selezionare lo **SKU del certificato** 
   
   Infine, selezionare lo SKU del certificato adatto alle proprie esigenze e fare clic su Crea. Attualmente, il Servizio app di Azure consente di acquistare due diverse SKU
   
        •    S1 – Standard Certificate with 1-year validity and auto renewal  
        •    W1 – Wild card Certificate with 1-year validity and auto renewal      
   Per altre informazioni vedere **[Prezzi di Servizio app](https://azure.microsoft.com/pricing/details/web-sites/)**.

![inserimento immagine del certificato SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [!NOTE]
> La creazione del certificato SSL richiederà da 1 a 10 minuti. Questo processo esegue più passaggi in background che sarebbero altrimenti molto complessi da eseguire manualmente.  
> 
> 

## <a name="a-namebkmkstorekeyvaultastep-1-store-the-certificate-in-azure-key-vault"></a><a name="bkmk_StoreKeyVault"></a>Passaggio 1: archiviare il certificato nell'insieme di credenziali delle chiavi di Azure
Questo passaggio descrive come archiviare un certificato SSL acquistato nell'insieme di credenziali delle chiavi di Azure desiderato.

1. Dopo aver completato l'acquisto del certificato SSL è necessario aprire manualmente il pannello delle risorse **Certificati del servizio app** passando nuovamente a questo pannello (vedere Passaggio 1)   
   
   ![inserimento immagine stato di pronto per l'archiviazione in un insieme di credenziali delle chiavi](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Si noterà che lo stato del certificato è **"Pending Issuance"** (Rilascio in sospeso) dal momento che sono previsti pochi altri passaggi da completare prima di iniziare a usare questo certificato.
2. Fare clic su **"Certificate Configuration"** (Configurazione del certificato) nel pannello Certificate Properties (Proprietà del certificato) e fare clic su **"Step 1: Store"** (Passaggio 1: archiviare) per archiviare il certificato nell'insieme di credenziali delle chiavi di Azure.
3. Nel pannello **"Key Vault Status"** (Stato dell'insieme di credenziali delle chiavi) fare clic su **"Key Vault Repository"** (Archivio dell'insieme di credenziali delle chiavi) per scegliere un insieme esistente in cui archiviare il certificato **O su "Create New Key Vault"** (Crea nuovo insieme di credenziali delle chiavi) per creare un nuovo insieme all'interno della stessa sottoscrizione e dello stesso gruppo di risorse.
   
   ![inserimento immagine della creazione di un nuovo insieme di credenziali delle chiavi](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > L'insieme di credenziali delle chiavi di Azure prevede addebiti minimi per l'archiviazione di questo certificato. Per altre informazioni, vedere **[Prezzi di Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/pricing/details/key-vault/)**.
   > 
   > 
4. Dopo aver selezionato l'archivio dell'insieme di credenziali delle chiavi in cui archiviare il certificato, procedere e archiviarlo facendo clic sul pulsante **"Archivia"** nella parte superiore del pannello **"Key Vault Status"** (Stato dell'insieme di credenziali delle chiavi).  
   
    Questa operazione conclude il passaggio di archiviazione del certificato acquistato nell'insieme di credenziali delle chiavi di Azure desiderato. Quando si aggiorna il pannello, dovrebbe essere presente un segno di spunta verde in corrispondenza anche di questo passaggio.

## <a name="a-namebkmkverifyownershipastep-2-verify-the-domain-ownership"></a><a name="bkmk_VerifyOwnership"></a>Passaggio 2: verificare la proprietà del dominio
Questo passaggio descrive come eseguire la verifica della proprietà del dominio per un certificato SSL appena ordinato. 

1. Fare clic su **"Step 2: Verify"** (Passaggio 2: verifica) nel pannello **"Certificate Configuration"** (Configurazione del certificato). Esistono 3 tipi di verifica del dominio supportati da Certificati del servizio app.
   
   * **Verifica del dominio** 
     
     * Questo è il processo più semplice **SOLO** in caso di **[acquisto del dominio personalizzato dal Servizio app di Azure.](custom-dns-web-site-buydomains-web-app.md)**
     * Fare clic sul pulsante **"Verifica"** per completare questo passaggio.
     * Fare clic su **"Aggiorna"** per aggiornare lo stato del certificato dopo aver completato la verifica. Il completamento della verifica potrebbe richiedere qualche minuto.
   * **Verifica tramite posta elettronica**
     
     * Un messaggio di posta elettronica di verifica è già stato inviato agli indirizzi di posta elettronica associati al dominio personalizzato.
     * Aprire il messaggio di posta elettronica e fare clic sul collegamento per la verifica per completare il passaggio di verifica tramite posta elettronica. 
     * Se è necessario un nuovo invio del messaggio di verifica, fare clic sul pulsante **"Invia di nuovo il messaggio di posta elettronica"** .
   * **Verifica manuale**    
     
      **Verifica del record TXT DNS**
        
        * Mediante il gestore DNS, creare un record TXT nel sottodominio **'DZC'** con valore uguale al **token di verifica del dominio.**
        * Fare clic su **"Aggiorna"** per aggiornare lo stato del certificato dopo aver completato la verifica. Il completamento della verifica potrebbe richiedere qualche minuto.
          
          Ad esempio, per eseguire la convalida di un certificato con caratteri jolly con nome host **\*.contosocertdemo.com** o **\*.subdomain.contosocertdemo.com** e token di verifica del dominio **cAGgQrKc**, è necessario creare un record TXT in dzc.contosocertdemo.com con valore **cAGgQrKc.**     

## <a name="a-namebkmkassigncertificateastep-3-assign-certificate-to-app-service-app"></a><a name="bkmk_AssignCertificate"></a>Passaggio 3: Assegnare il certificato all'app del servizio app
Questo passaggio descrive come assegnare un certificato appena acquistato alle app del servizio app. 

> [!NOTE]
> Prima di eseguire la procedura inclusa in questa sezione, è necessario avere associato un nome di dominio personalizzato all'app. Per altre informazioni, vedere **[Configurare un nome di dominio personalizzato nel servizio app di Azure.](web-sites-custom-domain-name.md)**
> 
> 

1. Accedere al **Portale di Azure[ dal browser.](https://portal.azure.com/)**
2. Fare clic sull'opzione **Servizio app** a sinistra nella pagina.
3. Fare clic sul nome dell'app a cui si desidera assegnare il certificato. 
4. In **Impostazioni** fare clic su **Certificati SSL**
5. Fare clic su **Importa il certificato del servizio app** e selezionare il certificato appena acquistato
   
   ![inserimento immagine dell'importazione del certificato](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)
6. Nella sezione **Associazioni SSL** fare clic su **Add bindings** (Aggiungi associazioni)
7. Nel pannello **Aggiungi associazione SSL** usare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da usare. È possibile anche stabilire se usare il metodo SSL basato su **[Indicazione nome server (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o su IP.
   
    ![inserimento immagine di associazioni SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
8. Fare clic su **Aggiungi l'associazione** per salvare le modifiche e abilitare SSL.

Se è stata selezionata l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario eseguire i passaggi aggiuntivi seguenti:

* Dopo la configurazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato all'app. È possibile trovare questo indirizzo IP nella pagina **Dominio personalizzato** nelle impostazioni dell'app, proprio sotto la sezione **Nomi host**. Sarà elencato come **Indirizzo IP esterno**
  
    ![inserimento immagine di IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
  
    Si noti che questo indirizzo IP sarà diverso rispetto all'indirizzo IP virtuale utilizzato in precedenza per la configurazione del record A per il dominio. Se è stato impostato l'uso del metodo SSL basato su SNI o se non è stato impostato l'uso del metodo SSL, per questa voce non verrà elencato alcun indirizzo.

1. Usando gli strumenti forniti dal registrar, modificare il record A per il nome di dominio personalizzato, in modo che faccia riferimento all'indirizzo IP riportato nel passaggio precedente.
   A questo punto si dovrebbe poter andare all'app usando HTTPS:// anziché HTTP:// per verificare che il certificato sia stato configurato correttamente.

## <a name="a-namebkmkrekeyarekey-and-sync-the-certificate"></a><a name="bkmk_Rekey"></a>Reimpostare e sincronizzare il certificato
1. Per motivi di sicurezza, sarà possibile reimpostare il certificato semplicemente selezionando l'opzione **"Reimposta e sincronizza"** nel pannello **"Proprietà del certificato"**. 
2. Fare clic sul pulsante **"Reimposta"** per avviare il processo. Questo processo può richiedere da 1 a 10 minuti. 
   
    ![inserimento immagine della reimpostazione SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)
3. Con la reimpostazione verrà emesso un nuovo certificato da parte dell'autorità di certificazione.
4. Non sono previsti addebiti per la reimpostazione delle chiavi per l'intera durata del certificato. 
5. La reimpostazione modifica lo stato del certificato su Rilascio in sospeso. 
6. Quando il certificato è pronto, accertarsi di sincronizzare le risorse usando questo certificato per evitare l'interruzione del servizio.
7. Non è disponibile l'opzione di sincronizzazione per i certificati non ancora assegnati all'app Web. 

## <a name="more-resources"></a>Altre risorse
* [Abilitare HTTPS per un'app in Azure App Service](web-sites-configure-ssl-certificate.md)
* [Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md)
* [Centro protezione Microsoft Azure](/support/trust-center/security/)
* [Opzioni di configurazione sbloccate in Siti Web di Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Portale di gestione di Azure](https://manage.windowsazure.com)

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 




<!--HONumber=Feb17_HO2-->


