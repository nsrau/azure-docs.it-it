---
title: Configurare SSL per un servizio cloud | Documentazione Microsoft
description: Informazioni su come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l&quot;applicazione. Questi esempi utilizzano il portale di Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5360387816cbbcd631114730fad8b7ce2c8c8aa6


---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurazione di SSL per un'applicazione in Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](cloud-services-configure-ssl-certificate-portal.md)
> * [Portale di Azure classico](cloud-services-configure-ssl-certificate.md)
> 

La crittografia SSL (Secure Socket Layer) è il metodo più diffuso per proteggere i dati inviati tramite Internet. In questa attività comune viene illustrato come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l'applicazione.

> [!NOTE]
> Le procedure in questa attività si applicano a Servizi cloud di Azure. Per Servizi app, vedere [questo articolo](../app-service-web/web-sites-configure-ssl-certificate.md).
> 

In questa attività viene usata una distribuzione di produzione. Alla fine di questo argomento vengono fornite informazioni sull'uso di una distribuzione di gestione temporanea.

Leggere [questo articolo](cloud-services-how-to-create-deploy-portal.md) se non è stato ancora creato un servizio cloud.

[!INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## <a name="step-1-get-an-ssl-certificate"></a>Passaggio 1: Ottenere un certificato SSL
Per configurare SSL per un'applicazione, è necessario prima ottenere un certificato SSL che sia stato firmato da un'Autorità di certificazione (CA), ovvero un ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al servizio cloud. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio cloudapp.net. È necessario acquistare un nome di dominio personalizzato da utilizzare per accedere al servizio. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per accedere all'applicazione. Se ad esempio il nome di dominio personalizzato è **contoso.com**, il certificato da richiedere alla CA è ***.contoso.com** o **www.contoso.com**.
* Per il certificato deve essere usata una crittografia di almeno 2048 bit.

Per eseguire delle prove, è possibile [creare](cloud-services-certs-create.md) e usare un certificato auto firmato. Un certificato autofirmato non è autenticato tramite una CA e può usare il dominio cloudapp.net come URL del sito Web. Nell'attività seguente, ad esempio, viene usato un certificato autofirmato in cui il nome comune è **sslexample.cloudapp.net**.

A questo punto, è necessario includere le informazioni sul certificato nei file di definizione e configurazione del servizio.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Passaggio 2: Modificare i file di definizione e configurazione del servizio
L'applicazione deve essere configurata per utilizzare il certificato ed è necessario aggiungere un endpoint HTTPS. Di conseguenza, è necessario aggiornare i file di definizione e configurazione del servizio.

1. Nell'ambiente di sviluppo aprire il file di definizione del servizio (CSDEF), aggiungere una sezione **Certificates** all'interno della sezione **WebRole** e includere le informazioni seguenti relative al certificato (e ai certificati intermedi):
   
   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                        storeLocation="LocalMachine" 
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```
   
   Nella sezione **Certificates** è definito il nome del certificato, il relativo percorso e il nome dell'archivio in cui è situato.
   
   Le autorizzazioni (attributo`permisionLevel` ) possono essere impostate su uno dei seguenti valori:
   
   | Valore di autorizzazione | Descrizione |
   | --- | --- |
   | limitedOrElevated |**(Predefinito)** Tutti i processi di ruolo possono accedere alla chiave privata. |
   | elevated |Solo i processi con autorizzazioni elevate possono accedere alla chiave privata. |

2. Nel file csdef aggiungere un elemento **InputEndpoint** all'interno della sezione **Endpoints** per abilitare HTTPS:
   
   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Nel file di definizione del servizio aggiungere un elemento **Binding** all'interno della sezione **Sites**. Viene aggiunto un binding HTTPS per il mapping dell'endpoint al sito:
   
   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```
   
   Tutte le modifiche necessarie al file di definizione del servizio sono state completate ma è ora necessario aggiungere le informazioni del certificato al file di configurazione del servizio.
4. Nel file di configurazione del servizio (CSCFG), ServiceConfiguration.Cloud.cscfg, aggiungere una sezione **Certificates** all'interno della sezione **Role**, sostituendo il valore di identificazione personale illustrato di seguito con quello del certificato:
   
   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate" 
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

Nell'esempio precedente viene usato **sha1** come algoritmo di identificazione personale. Specificare il valore appropriato per l'algoritmo di identificazione personale del certificato in uso.

Ora che i file di definizione e configurazione del servizio sono stati aggiornati, creare il pacchetto della distribuzione per il caricamento in Azure. Se si usa **cspack**, non usare il flag **/generateConfigurationFile**, poiché questo sovrascriverebbe le informazioni del certificato appena inserite.

## <a name="step-3-upload-a-certificate"></a>Passaggio 3: Caricare un certificato
Connettersi al portale e...

1. Nel portale selezionare il **servizio cloud**. Il servizio è disponibile nella sezione **Tutte le risorse**. 
   
    ![Pubblicare il servizio cloud](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Fare clic su **Certificati**.
   
    ![Fare clic sull'icona dei certificati](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Fornire il **File**, la **Password** e fare clic su **Carica**.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Passaggio 4: Connettersi all'istanza del ruolo usando HTTPS
Ora che la distribuzione è in esecuzione in Azure, è possibile connettersi a questa usando HTTPS.

1. Fare clic sull'**URL del sito** per aprire il Web browser.
   
   ![Fare clic sull'URL del sito](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Nel Web browser modificare il collegamento per usare **https** invece di **http**, quindi accedere alla pagina.
   
   > [!NOTE]
   > Se si usa un certificato autofirmato, quando si passa a un endpoint HTTPS con il certificato autofirmato associato, è possibile che nel browser venga visualizzato un errore del certificato. L'uso di un certificato firmato da un'Autorità di certificazione attendibile eliminerà il problema. Nel frattempo l'errore può essere ignorato. Un'altra opzione consiste nell'aggiungere il certificato autofirmato nell'archivio certificati dell'Autorità di certificazione attendibile dell'utente.
   > 
   > 
   
   ![Anteprima del sito](media/cloud-services-configure-ssl-certificate-portal/show-site.png)
   
   > [!TIP]
   > Se si desidera utilizzare SSL per una distribuzione di gestione temporanea anziché di produzione, è necessario innanzitutto determinare l'URL usato per la distribuzione di gestione temporanea. Una volta distribuito il servizio cloud, l'URL dell'ambiente di gestione temporanea è determinato dal GUID **ID distribuzione** nel formato seguente: `https://deployment-id.cloudapp.net/`  
   > 
   > Creare un certificato con il nome comune (CN) uguale all'URL basato su GUID, ad esempio,**328187776e774ceda8fc57609d404462.cloudapp.net**. Usare il portale per aggiungere il certificato al servizio cloud di gestione temporanea. Aggiungere le informazioni del certificato ai file CSDEF e CSCFG, ricreare il pacchetto dell'applicazione e aggiornare la distribuzione di gestione temporanea per usare il nuovo pacchetto.
   > 

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage-portal.md).



<!--HONumber=Nov16_HO3-->


