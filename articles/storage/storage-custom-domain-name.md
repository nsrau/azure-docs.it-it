---
title: Configurare un nome di dominio per l&quot;endpoint dell&quot;archiviazione BLOB | Microsoft Docs
description: Informazioni su come eseguire il mapping di un dominio utente personalizzato all&quot;endpoint di archiviazione BLOB per un account di archiviazione di Azure nel portale di Azure classico.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: fe4b9c356e5f7d56cb7e1fa62344095353d0b699
ms.openlocfilehash: 65ecd654cf6f1558a8f9ad6d6718ae5396fb52d0


---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB
## <a name="overview"></a>Panoramica
È possibile configurare un nome di dominio personalizzato per l'accesso ai dati BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per l'archiviazione BLOB è `<storage-account-name>.blob.core.windows.net`. Se si esegue il mapping di un dominio personalizzato e di un sottodominio come **www.contoso.com** all'endpoint BLOB per l'account di archiviazione, anche gli utenti potranno accedere ai dati BLOB dell'account di archiviazione usando tale dominio.

> [!IMPORTANT]
> Archiviazione di Azure non supporta ancora HTTPS con domini personalizzati. Siamo consapevoli del fatto che i nostri clienti sono interessati a questa funzionalità, che verrà resa disponibile in una versione futura.
>
>

Esistono due modi per puntare il dominio personalizzato all'endpoint BLOB per l'account di archiviazione. Il modo più semplice consiste nel creare un record CNAME per eseguire il mapping del dominio personalizzato e del sottodominio all'endpoint BLOB. Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso, il dominio di origine è rappresentato dal dominio personalizzato e dal relativo sottodominio. Si noti che il sottodominio è sempre obbligatorio. Il dominio di destinazione è l'endpoint del servizio BLOB.

Il processo di mapping del dominio personalizzato all'endpoint BLOB può tuttavia comportare un breve periodo di inattività del dominio durante la registrazione di quest'ultimo nel [portale di Azure classico](https://manage.windowsazure.com). Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, è possibile usare il sottodominio **asverify** di Azure per fornire un passaggio di registrazione intermedio in modo che gli utenti possano accedere al dominio durante l'applicazione del mapping DNS.

Nella tabella seguente sono illustrati gli URL di esempio per accedere ai dati BLOB in un account di archiviazione denominato **mystorageaccount**. Il dominio personalizzato registrato per l'account di archiviazione è **www.contoso.com**:

| Tipo di risorsa | Formato degli URL |
| --- | --- |
| Account di archiviazione |**URL predefinito:** http://mystorageaccount.blob.core.windows.net<p>**URL del dominio personalizzato:** http://www.contoso.com</td> |
| BLOB |**URL predefinito:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL del dominio personalizzato:** http://www.contoso.com/mycontainer/myblob |
| Contenitore radice |**URL predefinito:** http://mystorageaccount.blob.core.windows.net/myblob or http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**URL del dominio personalizzato:** http://www.contoso.com/myblob or http://www.contoso.com/$root/myblob |

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrazione di un dominio personalizzato per l'account di archiviazione
Eseguire questa procedura per registrare il dominio personalizzato se il fatto che il dominio sia temporaneamente non disponibile per gli utenti non costituisce un problema oppure se il dominio personalizzato al momento non ospita un'applicazione.

Se invece il dominio personalizzato supporta un'applicazione per cui non sono consentiti tempi di inattività, eseguire la procedura descritta in <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify</a>.

Per configurare un nome di dominio personalizzato, è necessario creare un nuovo record CNAME con il proprio registrar. Il record CNAME specifica un alias per un nome di dominio. In questo caso, viene usato per eseguire il mapping dell'indirizzo del dominio personalizzato all'endpoint dell'archiviazione BLOB per l'account di archiviazione.

Ogni registrar prevede un metodo simile ma leggermente diverso per specificare un record CNAME. Il concetto di base è tuttavia identico. Si noti che molti pacchetti di base di registrazione dei domini non offrono la configurazione dei DNS, quindi può essere necessario aggiornare il pacchetto prima di creare il record CNAME.

1. Nel [portale di Azure classico](https://manage.windowsazure.com)passare alla scheda **Archiviazione** .
2. Nella scheda **Storage** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.
3. Fare clic sulla scheda **Configure** .
4. Nella parte inferiore dello schermo fare clic su **Gestisci dominio** per visualizzare la finestra di dialogo **Gestisci dominio personalizzato**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME. Per questa procedura ignorare il testo che fa riferimento al sottodominio **asverify** .
5. Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.
7. Creare un nuovo record CNAME e specificare un alias di sottodominio, ad esempio **www** o **photos**. Specificare quindi un nome host, che corrisponde all'endpoint di servizio BLOB, nel formato **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Manage Custom Domain** .
8. Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Gestisci dominio personalizzato** e immettere il nome del dominio personalizzato, incluso il sottodominio, nel campo **Nome dominio personalizzato**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.
9. Fare clic sul pulsante **Register** per registrare il dominio personalizzato.

Se la registrazione viene completata correttamente, verrà visualizzato il messaggio **Your custom domain is active**. Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify
Usare questa procedura per registrare un dominio personalizzato che attualmente supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività. Creando un record CNAME che punta da asverify.&lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a asverify.&lt;accountarchiviazione&gt;.blob.core.windows.net, è possibile preregistrare il dominio con Azure. È quindi possibile creare un secondo record CNAME che punta da &lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a &lt;accountarchiviazione&gt;.blob.core.windows.net. A questo punto il traffico diretto al dominio personalizzato verrà indirizzato all'endpoint BLOB.

Il sottodominio asverify è un sottodominio speciale riconosciuto da Azure. Anteponendo **asverify** al proprio sottodominio, si consente ad Azure di riconoscere il dominio personalizzato senza modificare il relativo record DNS. Dopo aver modificato il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1. Nel [portale di Azure classico](https://manage.windowsazure.com)passare alla scheda **Archiviazione** .
2. Nella scheda **Storage** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.
3. Fare clic sulla scheda **Configure** .
4. Nella parte inferiore dello schermo fare clic su **Gestisci dominio** per visualizzare la finestra di dialogo **Gestisci dominio personalizzato**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME utilizzando il sottodominio **asverify** .
5. Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.
7. Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio asverify. Ad esempio, il formato del sottodominio specificato sarà **asverify.www** o **asverify.photos**. Specificare quindi un nome host, che corrisponde all'endpoint di servizio BLOB, nel formato **asverify.mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Manage Custom Domain** .
8. Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Gestisci dominio personalizzato** e immettere il nome del dominio personalizzato nel campo **Nome dominio personalizzato**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.
9. Selezionare la casella di controllo **Avanzate: usare il sottodominio ''asverify'' per preregistrare il dominio personalizzato.**.
10. Fare clic sul pulsante **Register** per preregistrare il dominio personalizzato.

    Se la preregistrazione viene completata correttamente, verrà visualizzato il messaggio **Your custom domain is active**.
11. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione. Per completare il processo, tornare al sito Web del registrar DNS e creare un altro record CNAME tramite cui eseguire il mapping del sottodominio all'endpoint del servizio BLOB. Specificare ad esempio il sottodominio **www** o **photos** e il nome host **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Con questo passaggio viene completata la registrazione del dominio personalizzato.
12. Infine, è possibile eliminare il record CNAME creato usando **asverify**, poiché è stato necessario solo come passaggio intermedio.

Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Verifica che il dominio personalizzato faccia riferimento all'endpoint del servizio BLOB
Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato effettivamente eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser utilizzare un URI nel formato seguente per accedere al BLOB:

    http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

È possibile ad esempio usare l'URI seguente per accedere a un Web Form tramite il sottodominio personalizzato **photos.contoso.com** che viene associato a un BLOB nel contenitore **myforms**:

    http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Annullare la registrazione di un dominio personalizzato dall'account di archiviazione
Per annullare la registrazione di un dominio personalizzato, seguire questa procedura:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel riquadro di spostamento fare clic su **Archiviazione**.
3. Nella pagina **Archiviazione** fare clic sul nome dell'account di archiviazione per visualizzare il dashboard.
4. Sulla barra multifunzione fare clic su **Gestisci dominio**.
5. Nella finestra di dialogo **Gestisci dominio personalizzato** fare clic su **Annulla registrazione**.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Come eseguire il mapping di un dominio personalizzato all'endpoint della rete per la distribuzione di contenuti (rete CDN)](../cdn/cdn-map-content-to-custom-domain.md)




<!--HONumber=Nov16_HO4-->


