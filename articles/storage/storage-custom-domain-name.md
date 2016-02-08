<properties
	pageTitle="Configurare un nome di dominio per l'endpoint di archiviazione BLOB | Microsoft Azure"
	description="Scoprire come eseguire il mapping di un dominio utente personalizzato all'endpoint di archiviazione BLOB per un account di archiviazione di Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2015"
	ms.author="tamram"/>


# Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB

## Panoramica

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per l'archiviazione BLOB è https://<*mystorageaccount*>.blob.core.windows.net. Se si esegue il mapping di un dominio personalizzato e di un sottodominio come **www.contoso.com** all'endpoint BLOB per l'account di archiviazione, anche gli utenti potranno accedere ai dati BLOB dell'account di archiviazione usando tale dominio.


> [AZURE.NOTE]	Le procedure in questa attività si applicano agli account di archiviazione di Azure. Per i servizi cloud, vedere <a href = "/develop/net/common-tasks/custom-dns/">Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure</a>. Per i siti Web, vedere <a href="/develop/net/common-tasks/custom-dns-web-site/">Configurazione di un nome di dominio personalizzato per un sito Web di Azure</a>.

Esistono due modi per puntare il dominio personalizzato all'endpoint BLOB per l'account di archiviazione. Il modo più semplice consiste nel creare un record CNAME per eseguire il mapping del dominio personalizzato e del sottodominio all'endpoint BLOB. Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso, il dominio di origine è rappresentato dal dominio personalizzato e dal relativo sottodominio. Si noti che il sottodominio è sempre obbligatorio. Il dominio di destinazione è l'endpoint del servizio BLOB.

Il processo di mapping del dominio personalizzato all'endpoint BLOB può tuttavia comportare un breve periodo di inattività del dominio durante la registrazione di quest'ultimo nel [portale di Azure classico](https://manage.windowsazure.com). Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, è possibile usare il sottodominio **asverify** di Azure per fornire un passaggio di registrazione intermedio in modo che gli utenti possano accedere al dominio durante l'applicazione del mapping DNS.

Nella tabella seguente sono illustrati gli URL di esempio per accedere ai dati BLOB in un account di archiviazione denominato **mystorageaccount**. Il dominio personalizzato registrato per l'account di archiviazione è **www.contoso.com**:

Tipo di risorsa|Formato degli URL
---|---
Account di archiviazione|**URL predefinito:** http://mystorageaccount.blob.core.windows.net<p>**URL del dominio personalizzato:** http://www.contoso.com</td>
BLOB|**URL predefinito:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL del dominio personalizzato:** http://www.contoso.com/mycontainer/myblob
Contenitore radice|**URL predefinito:** http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**URL del dominio personalizzato:** http://www.contoso.com/myblob o http://www.contoso.com/$root/myblob

## Registrazione di un dominio personalizzato per l'account di archiviazione

Eseguire questa procedura per registrare il dominio personalizzato se il fatto che il dominio sia temporaneamente non disponibile per gli utenti non costituisce un problema oppure se il dominio personalizzato al momento non ospita un'applicazione.

Se invece il dominio personalizzato supporta un'applicazione per cui non sono consentiti tempi di inattività, eseguire la procedura descritta in <a href="#register-asverify">Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify</a>.

Per configurare un nome di dominio personalizzato, è necessario creare un nuovo record CNAME con il proprio registrar. Il record CNAME specifica un alias per un nome di dominio. In questo caso, viene usato per eseguire il mapping dell'indirizzo del dominio personalizzato all'endpoint dell'archiviazione BLOB per l'account di archiviazione.

Ogni registrar prevede un metodo simile ma leggermente diverso per specificare un record CNAME. Il concetto di base è tuttavia identico. Si noti che molti pacchetti di base di registrazione dei domini non offrono la configurazione dei DNS, quindi può essere necessario aggiornare il pacchetto prima di creare il record CNAME.

1.  Nel [portale di Azure classico](https://manage.windowsazure.com)passare alla scheda **Archiviazione**.

2.  Nella scheda **Storage** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.

3.  Fare clic sulla scheda **Configura**.

4.  Nella parte inferiore dello schermo fare clic su **Manage Domain** per visualizzare la finestra di dialogo **Manage Custom Domain**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME. Per questa procedura ignorare il testo che fa riferimento al sottodominio **asverify**.

5.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.

6.  Individuare la sezione per la gestione dei record CNAME. Può essere necessario passare in una pagina di impostazioni avanzate e cercare il termine **CNAME**, **Alias** o **Subdomains**.

7.  Creare un nuovo record CNAME e specificare un alias di sottodominio, ad esempio **www** o **photos**. Specificare quindi un nome host, che corrisponde all'endpoint del servizio BLOB, nel formato **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Manage Custom Domain**.

8.  Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Manage Custom Domain** e immettere il nome del dominio personalizzato, incluso il sottodominio, nel campo **Custom Domain Name**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.

9. Fare clic sul pulsante **Register** per registrare il dominio personalizzato.

	Se la registrazione viene completata correttamente, verrà visualizzato il messaggio **Your custom domain is active**. Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify

Usare questa procedura per registrare un dominio personalizzato che attualmente supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività. Creando un record CNAME che punta da asverify.&lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a asverify.&lt;accountarchiviazione&gt;.blob.core.windows.net, è possibile preregistrare il dominio con Azure. È quindi possibile creare un secondo record CNAME che punta da &lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a &lt;accountarchiviazione&gt;.blob.core.windows.net. A questo punto il traffico diretto al dominio personalizzato verrà indirizzato all'endpoint BLOB.

Il sottodominio asverify è un sottodominio speciale riconosciuto da Azure. Anteponendo **asverify** al proprio sottodominio, si consente ad Azure di riconoscere il dominio personalizzato senza modificare il relativo record DNS. Dopo aver modificato il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1.  Nel [portale di Azure classico](https://manage.windowsazure.com)passare alla scheda **Archiviazione**.

2.  Nella scheda **Storage** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.

3.  Fare clic sulla scheda **Configura**.

4.  Nella parte inferiore dello schermo fare clic su **Manage Domain** per visualizzare la finestra di dialogo **Manage Custom Domain**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME utilizzando il sottodominio **asverify**.

5.  Accedere al sito Web del registrar DNS e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.

6.  Individuare la sezione per la gestione dei record CNAME. Può essere necessario passare in una pagina di impostazioni avanzate e cercare il termine **CNAME**, **Alias** o **Subdomains**.

7.  Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio asverify. Ad esempio, il formato del sottodominio specificato sarà **asverify.www** or **asverify.photos**. Specificare quindi un nome host, che corrisponde all'endpoint del servizio BLOB, nel formato **asverify.mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Manage Custom Domain**.

8.  Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Manage Custom Domain** e immettere il nome del dominio personalizzato nel campo **Custom Domain Name**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.

9.	Selezionare la casella di controllo **Avanzate: usare il sottodominio ''asverify'' per preregistrare il dominio personalizzato.**.

10. Fare clic sul pulsante **Register** per preregistrare il dominio personalizzato.

	Se la preregistrazione viene completata correttamente, verrà visualizzato il messaggio **Your custom domain is active**.

11. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione. Per completare il processo, tornare al sito Web del registrar DNS e creare un altro record CNAME tramite cui eseguire il mapping del sottodominio all'endpoint del servizio BLOB. Specificare ad esempio il sottodominio **www** o **photos** e il nome host **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Con questo passaggio viene completata la registrazione del dominio personalizzato.

12. Infine, è possibile eliminare il record CNAME creato usando **asverify**, poiché è stato necessario solo come passaggio intermedio.

Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## Verifica che il dominio personalizzato faccia riferimento all'endpoint del servizio BLOB

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato effettivamente eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser utilizzare un URI nel formato seguente per accedere al BLOB:

-   http://<*sottodominio.dominiopersonalizzato*>/<*contenitore*>/<*BLOB*>

Ad esempio, è possibile utilizzare l'URI seguente per accedere a un Web Form tramite il sottodominio personalizzato **photos.contoso.com** di cui viene eseguito il mapping a un BLOB nel contenitore **myforms**:

-   http://photos.contoso.com/myforms/applicationform.htm

## Risorse aggiuntive

-   <a href="http://msdn.microsoft.com/library/azure/gg680307.aspx">Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato</a>

<!---HONumber=AcomDC_0128_2016-->