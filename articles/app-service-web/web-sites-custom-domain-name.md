<properties
	pageTitle="Eseguire il mapping di un nome di dominio personalizzato in un'app di Azure"
	description="Informazioni su come eseguire il mapping di un nome di dominio personalizzato (dominio vanity) a un'app nel servizio app di Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="cephalin"/>

# Eseguire il mapping di un nome di dominio personalizzato in un'app di Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Questo articolo illustra come eseguire manualmente il mapping di un nome dominio personalizzato nell'app Web, nel back-end dell'app per dispositivi mobili o nell'app per le API nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

L'app è già dotata di un sottodominio univoco di azurewebsites.net. Se ad esempio l'app è denominata **contoso**, il nome dominio sarà **contoso.azurewebsites.net**. È tuttavia possibile eseguire il mapping di un nome dominio personalizzato all'app in modo che il relativo URL, ad esempio `www.contoso.com`, rifletta il proprio marchio.

>[AZURE.NOTE] Ottenere assistenza dagli esperti di Azure nei [forum di Azure](https://azure.microsoft.com/support/forums/). Per un livello di supporto ancora più elevato, visitare il [sito del Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Acquistare un nuovo dominio personalizzato nel portale di Azure

Se non è già stato acquistato un nome di dominio personalizzato, è possibile acquistarne uno e gestirlo direttamente nelle impostazioni dell'app nel [portale di Azure](https://portal.azure.com). Questa opzione semplifica l'esecuzione del mapping di un dominio personalizzato all'app, a prescindere che l'app usi [Gestione traffico di Azure](web-sites-traffic-manager-custom-domain-name.md) o meno.

Per istruzioni, vedere [Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md).

## Eseguire il mapping di un dominio personalizzato acquistato esternamente

Se è già stato acquistato un dominio personalizzato dal [Servizio DNS di Azure](https://azure.microsoft.com/services/dns/) o da un provider di terze parti, il mapping del dominio personalizzato all'app prevede tre passaggi:

1. [*(Solo un record)* Ottenere l'indirizzo IP dell'app](#vip).
1. [Abilitare il nome di dominio personalizzato per l'app Azure](#enable).
    - **Dove**: nel [portale di Azure](https://portal.azure.com).
    - **Perché**: in modo che l'app possa rispondere alle richieste inviate al nome di dominio personalizzato.
2. [Creare i record DNS che eseguono il mapping del dominio all'app](#dns).
    - **Dove**: nello strumento di gestione del registrar, ad esempio DNS Azure, www.godaddy.com e così via.
    - **Perché**: in modo che il registrar possa risolvere il dominio personalizzato sull'app Azure.
3. [Verificare la propagazione di DNS](#verify).

### Tipi di domini per i quali è possibile eseguire il mapping

Il servizio app di Azure consente di eseguire il mapping delle categorie di domini personalizzati seguenti all'app.

- **Dominio radice**: il dominio radice è il dominio riservato con il registrar, rappresentato di solito dal record host `@`, ad esempio **contoso.com**.
- **Sottodominio**: qualsiasi dominio che si trova sotto il dominio radice. Ad esempio **www.contoso.com**, rappresentato dal record host `www`. È possibile eseguire il mapping di più sottodomini dello stesso dominio radice ad app differenti in Azure.
- **Dominio con caratteri jolly**: [qualsiasi sottodominio la cui etichetta DNS più a sinistra è `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record), ad esempio i record host `*` e `*.blogs`. ad esempio ***.contoso.com**.

### Tipi di record DNS utilizzabili

A seconda delle esigenze, è possibile usare due diversi tipi di record DNS standard per eseguire il mapping del dominio personalizzato:

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A): esegue il mapping del nome dominio personalizzato direttamente all'indirizzo IP virtuale dell'app Azure.
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record): esegue il mapping del nome dominio personalizzato al nome dominio di Azure dell'app, **& lt;*appname*>.azurewebsites.net**.

Il vantaggio di CNAME è la sua persistenza a prescindere dalle variazioni dell'indirizzo IP. L'indirizzo IP virtuale dell'app può cambiare se si elimina e si crea nuovamente l'app oppure se da un piano tariffario di livello superiore si torna al piano **Condiviso**. Se l'indirizzo IP cambia, un record CNAME resta valido, mentre un record A deve essere aggiornato.

Questa esercitazione illustra i passaggi per l'uso del record A e del record CNAME.

<a name="vip"></a>
## Passaggio 1. *(Solo un record)* Ottenere l'indirizzo IP dell'app
Per eseguire il mapping di un nome dominio personalizzato usando un record A è necessario l'indirizzo IP dell'app Azure. Se invece si esegue il mapping usando un record CNAME, ignorare questo passaggio e passare alla sezione successiva.

1.	Accedere al [Portale di Azure](https://portal.azure.com).
2.	Fare clic su **Servizi app** nel menu a sinistra.
4.	Fare clic sull'app, quindi su **Impostazioni** > **Domini personalizzati ed SSL** > **Porta domini esterni**.
5.	In **Nomi di dominio** digitare il nome di dominio personalizzato.
6.  Prendere nota dell'indirizzo IP da usare in seguito.
7.  Tenere aperto questo pannello del portale. Verrà usato di nuovo dopo aver creato i record DNS.

<a name="dns"></a>
## Passaggio 2. Creare i record DNS

Accedere al registrar di dominio e usare i relativi strumenti per aggiungere un record A o CNAME. L'interfaccia utente di ogni registrar è leggermente diversa, quindi è consigliabile consultare la documentazione del provider. Di seguito sono tuttavia indicate alcune linee guida generali.

1.	Individuare la pagina relativa alla gestione dei record DNS. Individuare collegamenti o aree del sito denominate **Nome dominio**, **DNS** o **Name Server Management**. Spesso è possibile individuare un collegamento nelle informazioni dell'account, cercando una voce simile a **My domains**.
2.	Cercare un collegamento che consenta di aggiungere o modificare i record DNS. Tale collegamento può essere denominato **File di zona** o **Record DNS** oppure può trattarsi di un collegamento **Avanzate** per la configurazione.
3.  Creare il record e salvare le modifiche.
    - [Istruzioni per un record A](#a).
    - [Istruzioni per un record CNAME](#cname).

<a name="a"></a>
### Creare un record A

Per usare un record A per eseguire il mapping all'indirizzo IP dell'app Azure è in realtà necessario creare sia un record A che un record CNAME. Il record A è per la risoluzione DNS in sé, mentre il record CNAME consente ad Azure di verificare che l'utente possieda il nome di dominio personalizzato.

Il record A deve essere configurato come indicato di seguito. @ rappresenta in genere il dominio principale:
 
<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host/Nome/Nome host</th>
    <th>Valore</th>
  </tr>
  <tr>
    <td>contoso.com (radice)</td>
    <td>@</td>
    <td>Indirizzo IP del [passaggio 1]()</td>
  </tr>
  <tr>
    <td>www.contoso.com (sottodominio)</td>
    <td>www</td>
    <td>Indirizzo IP del [passaggio 1]()</td>
  </tr>
  <tr>
    <td>*.contoso.com (carattere jolly)</td>
    <td>*</td>
    <td>Indirizzo IP del [passaggio 1]()</td>
  </tr>
</table>

Il record CNAME aggiuntivo segue la convenzione che esegue il mapping da awverify.&lt;*sottodominio*>.&lt;*dominioradice*> a awverify.&lt;*sottodominio*>.azurewebsites.net. Vedere gli esempi seguenti:

<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host/Nome/Nome host</th>
    <th>Valore</th>
  </tr>
  <tr>
    <td>contoso.com (radice)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sottodominio)</td>
    <td>awverify.www</td>
    <td>awverify.www.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (carattere jolly)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### Creare un record CNAME

Se si usa un record CNAME per eseguire il mapping al nome di dominio predefinito dell'app Azure, non è necessario un record CNAME aggiuntivo come nel caso di un record A.

Il record CNAME deve essere configurato come indicato di seguito. @ rappresenta in genere il dominio radice:

<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host/Nome/Nome host</th>
    <th>Valore</th>
  </tr>
  <tr>
    <td>contoso.com (radice)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sottodominio)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (carattere jolly)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>


>[AZURE.NOTE] È possibile usare il DNS di Azure per ospitare i record di dominio necessari per l'app Web. Per configurare il dominio personalizzato e creare i record in DNS di Azure, vedere [Creare record DNS personalizzati per un'app Web](../dns/dns-web-sites-custom-domain.md).

<a name="enable"></a>
## Passaggio 3. Abilitare il nome di dominio personalizzato per l'app

Nel pannello **Porta domini esterni** del portale di Azure (vedere il [passaggio 1](#vip)) è necessario aggiungere all'elenco il nome di dominio completo (FQDN) del dominio personalizzato.

1.	Tornare al pannello **Porta domini esterni** nel portale di Azure.

2.	Aggiungere il nome di dominio completo del dominio personalizzato all'elenco, ad esempio **www.contoso.com**.

    >[AZURE.NOTE] Azure tenterà di verificare il nome di dominio usato qui, quindi è necessario assicurarsi che sia lo stesso nome di dominio per il quale è stato creato un record DNS nel [passaggio 2](#dns). Se si è sicuri che

6.  Fare clic su **Save**.

7.  Dopo aver configurato correttamente il nuovo nome di dominio personalizzato, passare al nome di dominio personalizzato in un browser. Sarà visibile l'app in esecuzione

<a name="verify"></a>
## Verificare la propagazione di DNS

Dopo aver completato i passaggi di configurazione, la propagazione delle modifiche può richiedere del tempo, a seconda del provider DNS. È possibile verificare che la propagazione DNS funziona come previsto con [http://digwebinterface.com/](http://digwebinterface.com/). Dopo essere andati nel sito, specificare i nomi host nella casella di testo e fare clic su **approfondire**. Verificare i risultati per verificare che le modifiche recenti siano state applicate.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] La propagazione delle voci DNS può richiedere fino a 48 ore, a volte più. Se tutto è stato configurato correttamente, è necessario attendere che la propagazione abbia esito positivo.

## Passaggi successivi

Per altre informazioni, vedere: [Introduzione a DNS di Azure](../dns/dns-getstarted-create-dnszone.md) e [Delegare un dominio a DNS di Azure](../dns/dns-domain-delegation.md)

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0727_2016-->