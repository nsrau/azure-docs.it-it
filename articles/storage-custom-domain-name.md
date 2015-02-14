<properties 
	pageTitle="Configurare un nome di dominio per i dati BLOB in un account di archiviazione | Microsoft Azure" 
	description="Informazioni su come configurare un dominio personalizzato per l'accesso ai dati BLOB in un account di archiviazione di Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2014" 
	ms.author="tamram"/>


# Configurare un nome di dominio personalizzato per i dati BLOB in un account di archiviazione di Azure
È possibile configurare un nome di dominio personalizzato per l'accesso ai dati BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per il servizio BLOB è https://<*mystorageaccount*>.blob.core.windows.net. Se si esegue il mapping di un dominio personalizzato e di un sottodominio come **www.contoso.com** all'endpoint BLOB per l'account di archiviazione, anche gli utenti potranno accedere ai dati BLOB dell'account di archiviazione usando tale dominio. 


> [AZURE.NOTE]	Le procedure in questa attività si applicano agli account di archiviazione di Azure. Per i servizi cloud, vedere <a href = "/it-it/develop/net/common-tasks/custom-dns/">Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure</a>. Per i siti Web, vedere <a href = "/it-it/develop/net/common-tasks/custom-dns/">Configurazione di un nome di dominio personalizzato per un sito Web di Azure</a>. 

> [AZURE.NOTE]	Un account di archiviazione premium non può essere mappato a un nome di dominio personalizzato. Vedere la pagina [Archiviazione premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=521898) per informazioni sugli account di archiviazione premium.

Esistono due modi per puntare il dominio personalizzato all'endpoint BLOB per l'account di archiviazione. Il modo più semplice consiste nel creare un record CNAME per eseguire il mapping del dominio personalizzato e del sottodominio all'endpoint BLOB. Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso, il dominio di origine è rappresentato dal dominio personalizzato e dal relativo sottodominio. Si noti che il sottodominio è sempre obbligatorio. Il dominio di destinazione è l'endpoint del servizio BLOB.

Il processo di mapping del dominio personalizzato all'endpoint BLOB può tuttavia comportare un breve periodo di inattività del dominio durante la registrazione di quest'ultimo nel portale di gestione di Azure. Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, è possibile usare il sottodominio **asverify** di Azure per fornire un passaggio di registrazione intermedio in modo che gli utenti possano accedere al dominio durante l'applicazione del mapping DNS.

Nella tabella seguente sono illustrati gli URL di esempio per accedere ai dati BLOB in un account di archiviazione denominato **mystorageaccount**. Il dominio personalizzato registrato per l'account di archiviazione è **www.contoso.com**:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td style="width: 100px;"><strong>Tipo di risorsa</strong></td>
			<td><strong>Formati degli URL</strong></td>
		</tr>
		<tr>
			<td>Account di archiviazione</td>
			<td><strong>URL predefinito:</strong>http://mystorageaccount.blob.core.windows.net<br />
			<strong>URL del dominio personalizzato:</strong> http://www.contoso.com</td>
		</tr>
		<tr>
			<td>BLOB</td>
			<td><strong>URL predefinito:</strong>http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br /><strong>URL del dominio personalizzato:</strong>
			http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr>
			<td>Contenitore radice</td>
			<td><strong>URL predefinito:</strong>http://mystorageaccount.blob.core.windows.net/myblob
			<br/>oppure<br />
			http://mystorageaccount.blob.core.windows.net/$root/myblob<br />
			<strong>URL del dominio personalizzato:</strong>http://www.contoso.com/myblob
			<br/>oppure<br />
			http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>

In questo argomento verranno illustrate le attività seguenti:



- <a href="#register-domain">Registrazione di un dominio personalizzato per l'account di archiviazione</a>
- <a href="#register-asverify">Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify</a>
- <a name="#verify-subdomain">Verifica che il dominio personalizzato faccia riferimento all'endpoint del servizio BLOB</a>

<h2><a name="register-domain"></a>Registrazione di un dominio personalizzato per l'account di archiviazione</h2>

Eseguire questa procedura per registrare il dominio personalizzato se il fatto che il dominio sia temporaneamente non disponibile per gli utenti non costituisce un problema oppure se il dominio personalizzato al momento non ospita un'applicazione. 

Se invece il dominio personalizzato supporta un'applicazione per cui non sono consentiti tempi di inattività, eseguire la procedura descritta in <a href="#register-asverify">Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify</a>.

Per configurare un nome di dominio personalizzato, è necessario creare un nuovo record CNAME con il proprio registrar. Il record CNAME specifica un alias per il nome di dominio. In questo caso viene usato per eseguire il mapping dell'indirizzo del dominio personalizzato all'endpoint del servizio BLOB per l'account di archiviazione.

Ogni registrar prevede un metodo simile ma leggermente diverso per specificare un record CNAME. Il concetto di base,
è tuttavia identico. Si noti che molti pacchetti di base di registrazione dei domini non offrono la configurazione dei DNS, quindi può essere necessario aggiornare il pacchetto prima di creare il record CNAME. 

1.  Nel portale di gestione di Azure passare alla scheda **Archiviazione**.

2.  Nella scheda **Archiviazione** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.

3.  Fare clic sulla scheda **Configura**.

4.  Nella parte inferiore dello schermo fare clic su **Gestisci dominio** per visualizzare la finestra di dialogo **Gestisci dominio personalizzato**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME. Per questa procedura ignorare il testo che fa riferimento al sottodominio **asverify**.

5.  Accedere al sito Web del registrar DNS e passare alla pagina di
    gestione dei DNS. L'opzione potrebbe trovarsi in una sezione come **Dominio
    Nome**, **DNS** o **Gestione nome server**.

6.  Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una
    pagina di impostazioni avanzate e cercare i termini **CNAME**, **Alias**
    o **Sottodomini**.

7.  Creare un nuovo record CNAME e specificare un alias di sottodominio, ad esempio **www** o **photos**. Specificare
    quindi un nome host, che corrisponde all'endpoint del servizio BLOB, nel formato **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Gestisci dominio personalizzato**.

8.  Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Gestisci dominio personalizzato** e immettere il nome del dominio personalizzato, incluso il sottodominio, nel campo **Nome dominio personalizzato**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.

9. Fare clic sul pulsante **Registra** per registrare il dominio personalizzato. 

	Se la registrazione viene completata correttamente, verrà visualizzato il messaggio **Il dominio personalizzato è attivo**. Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate. 

<h2><a name="register-asverify"></a>Registrazione di un dominio personalizzato per l'account di archiviazione usando il sottodominio intermedio asverify</h2>

Usare questa procedura per registrare un dominio personalizzato che attualmente supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività. Creando un record CNAME che punta da asverify.&lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a asverify.&lt;accountarchiviazione&gt;.blob.core.windows.net, è possibile preregistrare il dominio con Azure. È quindi possibile creare un secondo record CNAME che punta da &lt;sottodominio&gt;.&lt;dominiopersonalizzato&gt; a &lt;accountarchiviazione&gt;.blob.core.windows.net. A questo punto il traffico diretto al dominio personalizzato verrà indirizzato all'endpoint BLOB.

Il sottodominio asverify è un sottodominio speciale riconosciuto da Azure. Anteponendo **asverify** al proprio sottodominio, si consente ad Azure di riconoscere il dominio personalizzato senza modificare il relativo record DNS. Dopo aver modificato il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1.  Nel portale di gestione di Azure passare alla scheda **Archiviazione**.

2.  Nella scheda **Archiviazione** fare clic sul nome dell'account di archiviazione di cui eseguire il mapping al dominio personalizzato.

3.  Fare clic sulla scheda **Configura**.

4.  Nella parte inferiore dello schermo fare clic su **Gestisci dominio** per visualizzare la finestra di dialogo **Gestisci dominio personalizzato**. Nella parte superiore della finestra di dialogo verranno visualizzate informazioni su come creare il record CNAME usando il sottodominio **asverify**.

5.  Accedere al sito Web del registrar DNS e passare alla pagina di
    gestione dei DNS. L'opzione potrebbe trovarsi in una sezione come **Dominio
    Nome**, **DNS** o **Gestione nome server**.

6.  Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una
    pagina di impostazioni avanzate e cercare i termini **CNAME**, **Alias**
    o **Sottodomini**.

7.  Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio asverify. Ad esempio, il formato del sottodominio specificato sarà **asverify.www** o **asverify.photos**. Specificare
    quindi un nome host, che corrisponde all'endpoint del servizio BLOB, nel formato **asverify.mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il formato del nome host viene definito automaticamente nella finestra di dialogo **Gestisci dominio personalizzato**.

8.  Dopo aver creato il record CNAME, tornare nella finestra di dialogo **Gestisci dominio personalizzato** e immettere il nome del dominio personalizzato nel campo **Nome dominio personalizzato**. Se ad esempio il dominio è **contoso.com** e il sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos**, immettere **photos.contoso.com**. Si noti che il sottodominio è obbligatorio.

9.	Fare clic sulla casella di controllo **Advanced: Usare il sottodominio 'asverify' per preregistrare il dominio personalizzato**. 

10. Fare clic sul pulsante **Registra** per preregistrare il dominio personalizzato. 

	Se la preregistrazione viene completata correttamente, verrà visualizzato il messaggio **Il dominio personalizzato è attivo**. 

11. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione. Per completare il processo, tornare al sito Web del registrar DNS e creare un altro record CNAME tramite cui eseguire il mapping del sottodominio all'endpoint del servizio BLOB. Specificare ad esempio il sottodominio **www** o **photos** e il nome **host mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Con questo passaggio viene completata la registrazione del dominio personalizzato.

12. Infine, è possibile eliminare il record CNAME creato usando **asverify**, poiché è stato necessario solo come passaggio intermedio.

Gli utenti possono a questo punto visualizzare i dati BLOB nel dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

<a name="verify-subdomain"> </a>

<h2>Verifica che il dominio personalizzato faccia riferimento all'endpoint del servizio BLOB</h2>

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato effettivamente eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser usare un URI nel formato seguente per accedere al BLOB:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Ad esempio, è possibile usare l'URI seguente per accedere a un Web Form tramite il sottodominio personalizzato
**photos.contoso.com** mappato a un BLOB nel proprio contenitore
**myforms**:

-   http://photos.contoso.com/myforms/applicationform.htm

## Risorse aggiuntive

-   <a href="http://msdn.microsoft.com/it-it/library/windowsazure/gg680307.aspx">Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato</a>
<!--HONumber=42-->
