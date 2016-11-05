---
title: Eseguire il mapping di un nome di dominio personalizzato in un'app di Azure
description: Informazioni su come eseguire il mapping di un nome di dominio personalizzato (dominio vanity) a un'app nel servizio app di Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: jimbe
tags: top-support-issue

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: cephalin

---
# Eseguire il mapping di un nome di dominio personalizzato in un'app di Azure
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Questo articolo illustra come eseguire manualmente il mapping di un nome dominio personalizzato nell'app Web, nel back-end dell'app per dispositivi mobili o nell'app per le API nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

L'app è già dotata di un sottodominio univoco di azurewebsites.net. Se ad esempio l'app è denominata **contoso**, il nome di dominio sarà **contoso.azurewebsites.net**. È tuttavia possibile eseguire il mapping di un nome dominio personalizzato all'app in modo che il relativo URL, ad esempio `www.contoso.com`, rifletta il proprio marchio.

> [!NOTE]
> Ottenere assistenza dagli esperti di Azure nei [forum di Azure](https://azure.microsoft.com/support/forums/). Per un livello di supporto ancora più elevato, passare al [sito del Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.
> 
> 

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Acquistare un nuovo dominio personalizzato nel portale di Azure
Se non è già stato acquistato un nome di dominio personalizzato, è possibile acquistarne uno e gestirlo direttamente nelle impostazioni dell'app nel [portale di Azure](https://portal.azure.com). Questa opzione semplifica l'esecuzione del mapping di un dominio personalizzato all'app, a prescindere che l'app usi [Gestione traffico di Azure](web-sites-traffic-manager-custom-domain-name.md) o meno.

Per istruzioni, vedere [Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md).

## Eseguire il mapping di un dominio personalizzato acquistato esternamente
Se è già stato acquistato un dominio personalizzato dal [Servizio DNS di Azure](https://azure.microsoft.com/services/dns/) o da un provider di terze parti, il mapping del dominio personalizzato all'app prevede tre passaggi:

1. [*(Solo un record)* Ottenere l'indirizzo IP dell'app](#vip).
2. [Creare i record DNS per il mapping del dominio all'app](#createdns).
   * **Dove**: nello strumento di gestione del registrar, ad esempio il servizio DNS di Azure, www.godaddy.com e così via.
   * **Perché**: in modo che il registrar possa risolvere il dominio personalizzato nell'app Azure.
3. [Abilitare il nome di dominio personalizzato per l'app Azure](#enable).
   * **Dove**: nel [portale di Azure](https://portal.azure.com).
   * **Perché**: in modo che l'app possa rispondere alle richieste inviate al nome di dominio personalizzato.
4. [Verificare la propagazione di DNS](#verify).

### Tipi di domini per i quali è possibile eseguire il mapping
Il servizio app di Azure consente di eseguire il mapping delle categorie di domini personalizzati seguenti all'app.

* **Dominio radice**: il dominio radice è il dominio riservato con il registrar, rappresentato di solito dal record host `@`, ad esempio **contoso.com**.
* **Sottodominio**: qualsiasi dominio che si trova sotto il dominio radice. Ad esempio **www.contoso.com**, rappresentato dal record host `www`. È possibile eseguire il mapping di più sottodomini dello stesso dominio radice ad app differenti in Azure.
* **Dominio con caratteri jolly**: [qualsiasi sottodominio la cui etichetta DNS più a sinistra è `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record), ad esempio i record host `*` e `*.blogs`. ad esempio ***.contoso.com**.

### Tipi di record DNS utilizzabili
A seconda delle esigenze, è possibile usare due diversi tipi di record DNS standard per eseguire il mapping del dominio personalizzato:

* [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A): esegue il mapping del nome dominio personalizzato direttamente all'indirizzo IP virtuale dell'app Azure.
* [CNAME](https://en.wikipedia.org/wiki/CNAME_record): esegue il mapping del nome dominio personalizzato al nome dominio di Azure dell'app, **& lt;*appname*>.azurewebsites.net**.

Il vantaggio di CNAME è la sua persistenza a prescindere dalle variazioni dell'indirizzo IP. Se si elimina e si ricrea l'app oppure se da un piano tariffario di livello superiore si torna al piano **Condiviso**, l'indirizzo IP virtuale dell'app potrebbe cambiare. Se l'indirizzo IP cambia, un record CNAME resta valido, mentre un record A richiede un aggiornamento.

Questa esercitazione illustra i passaggi per l'uso del record A e del record CNAME.

> [!IMPORTANT]
> Non creare un record CNAME per il dominio radice, ovvero il "record radice". Per altre informazioni, vedere [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain) (Perché non è possibile usare un record CNAME nel dominio radice). Per eseguire il mapping di un dominio radice all'app Azure, usare un record A.
> 
> 

<a name="vip"></a>

## Passaggio 1. *(Solo record A)* Ottenere l'indirizzo IP dell'app
Per eseguire il mapping di un nome dominio personalizzato usando un record A è necessario l'indirizzo IP dell'app Azure. Se invece si esegue il mapping usando un record CNAME, ignorare questo passaggio e passare alla sezione successiva.

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **Servizi app** nel menu a sinistra.
3. Fare clic sull'app, quindi su **Domini personalizzati**.
4. Prendere nota dell'indirizzo IP sopra la sezione Nomi host.
   
   ![Eseguire il mapping del nome di dominio personalizzato al record A: ottenere l'indirizzo IP per l'app del servizio app di Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)
5. Tenere aperto questo pannello del portale. Verrà usato di nuovo dopo aver creato i record DNS.

<a name="createdns"></a>

## Passaggio 2. Creare i record DNS
Accedere al registrar di dominio e usare i relativi strumenti per aggiungere un record A o CNAME. L'interfaccia utente di ogni registrar è leggermente diversa, quindi è consigliabile consultare la documentazione del provider. Di seguito sono tuttavia indicate alcune linee guida generali.

1. Individuare la pagina relativa alla gestione dei record DNS. Individuare collegamenti o aree del sito denominate **Nome dominio**, **DNS** o **Name Server Management**. Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**.
2. Cercare un collegamento che consenta di aggiungere o modificare i record DNS. Questo collegamento potrebbe essere costituito da un **File di zona** o i **Record DNS** oppure può trattarsi di un collegamento di configurazione **avanzato**.
3. Creare il record e salvare le modifiche.
   * [Istruzioni per un record A](#a).
   * [Istruzioni per un record CNAME](#cname).

<a name="a"></a>

### Creare un record A
Per usare un record A per eseguire il mapping all'indirizzo IP dell'app Azure, è in realtà necessario creare sia un record A che un record TXT. Il record A è per la risoluzione DNS in sé, mentre il record TXT consente ad Azure di verificare che l'utente possieda il nome di dominio personalizzato.

Configurare il record A come indicato di seguito. @ rappresenta in genere il dominio radice:

<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host A</th>
    <th>Valore A</th>
  </tr>
  <tr>
    <td>contoso.com (radice)</td>
    <td>@</td>
    <td>Indirizzo IP del <a href="#vip">passaggio 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sottodominio)</td>
    <td>www</td>
    <td>Indirizzo IP del <a href="#vip">passaggio 1</a></td>
  </tr>
  <tr>
    <td>*.contoso.com (carattere jolly)</td>
    <td>*</td>
    <td>Indirizzo IP del <a href="#vip">passaggio 1</a></td>
  </tr>
</table>

Il record TXT aggiuntivo segue la convenzione che esegue il mapping da &lt;*sottodominio*>.&lt;*dominioradice*> a &lt;*nomeapp*>.azurewebsites.net. Configurare il record TXT come indicato di seguito:

<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host TXT</th>
    <th>Valore TXT</th>
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

<a name="cname"></a>

### Creare un record CNAME
Se si usa un record CNAME per eseguire il mapping al nome di dominio predefinito dell'app Azure, non è necessario un record TXT aggiuntivo come nel caso di un record A.

> [!IMPORTANT]
> Non creare un record CNAME per il dominio radice, ovvero il "record radice". Per altre informazioni, vedere [Why can't a CNAME record be used at the root domain](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain) (Perché non è possibile usare un record CNAME nel dominio radice). Per eseguire il mapping di un dominio radice all'app Azure, usare un [record A](#a).
> 
> 

Configurare il record CNAME come indicato di seguito. @ rappresenta in genere il dominio radice:

<table cellspacing="0" border="1">
  <tr>
    <th>Esempio di FQDN</th>
    <th>Host CNAME</th>
    <th>Valore CNAME</th>
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

<a name="enable"></a>

## Passaggio 3. Abilitare il nome di dominio personalizzato per l'app
Nel pannello **Domini personalizzati** del portale di Azure (vedere il [passaggio 1](#vip)) è necessario aggiungere all'elenco il nome di dominio completo (FQDN) del dominio personalizzato.

1. Se non è già stato fatto, accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Servizi app** nel menu a sinistra.
3. Fare clic sull'app, quindi su **Domini personalizzati** > **Aggiungi il nome host**.
4. Aggiungere il nome di dominio completo del dominio personalizzato all'elenco, ad esempio **www.contoso.com**.
   
   ![Eseguire il mapping di un nome di dominio personalizzato a un'app Azure: aggiungere il nome di dominio all'elenco di nomi di dominio](./media/web-sites-custom-domain-name/add-custom-domain.png)
   
   > [!NOTE]
   > Azure tenterà di verificare il nome di dominio usato. Assicurarsi che si tratti dello stesso nome di dominio per cui è stato creato un record DNS nel [passaggio 2](#createdns).
   > 
   > 
5. Fare clic su **Convalida**.
6. Dopo aver fatto clic su **Convalida**, Azure avvierà il flusso di lavoro Verifica del dominio, che controllerà la proprietà del dominio oltre alla disponibilità del nome host e segnalerà la riuscita o un errore dettagliato con le linee guida consigliate per correggere l'errore.
7. Se la convalida avrà esito positivo, il pulsante **Aggiungi il nome host** diventerà attivo e sarà possibile assegnare il nome host.
8. Al termine della configurazione del nuovo nome di dominio personalizzato, passare al nome di dominio personalizzato in un browser. Se l'app Azure viene aperta dal browser, il nome di dominio personalizzato è configurato correttamente.

> [!NOTE]
> Se il record DNS è già in uso, in uno scenario di dominio attivo che gestisce il traffico, ed è necessario associarvi preventivamente l'app Web per la verifica del dominio, creare semplicemente un record TXT come negli esempi illustrati nella tabella seguente. Il record TXT aggiuntivo segue la convenzione che esegue il mapping da &lt;*sottodominio*>.&lt;*dominioradice*> a &lt;*nomeapp*>.azurewebsites.net.
> 
> <table cellspacing="0" border="1">
> 
> <tr>
> 
> <th>Esempio di FQDN</th>
> 
> <th>Host TXT</th>
> 
> <th>Valore TXT</th>
> </tr>
> 
> <tr>
> 
> <td>contoso.com (radice)</td>
> 
> <td>awverify.contoso.com</td>
> 
> <td>&lt;<i>appname</i>>.azurewebsites.net</td>
> </tr>
> 
> <tr>
> 
> <td>www.contoso.com (sottodominio)</td>
> 
> <td>awverify.www.contoso.com</td>
> 
> <td>&lt;<i>appname</i>>.azurewebsites.net</td>
> </tr>
> 
> <tr>
> 
> <td>*.contoso.com (sottodominio)</td>
> 
> <td>awverify.*.contoso.com</td>
> 
> <td>&lt;<i>appname</i>>.azurewebsites.net</td>
> </tr>
> </table>
> Dopo aver creato il record DNS, tornare al portale di Azure e aggiungere il nome di dominio personalizzato all'app Web.
> 
> 

<a name="verify"></a>

## Verificare la propagazione di DNS
Dopo aver completato i passaggi di configurazione, la propagazione delle modifiche può richiedere del tempo, a seconda del provider DNS. È possibile verificare che la propagazione DNS funziona come previsto con [http://digwebinterface.com/](http://digwebinterface.com/). Dopo essere andati nel sito, specificare i nomi host nella casella di testo e fare clic su **approfondire**. Verificare i risultati per verificare che le modifiche recenti siano state applicate.

![Eseguire il mapping di un nome di dominio personalizzato a un'app Azure: verificare la propagazione del DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [!NOTE]
> La propagazione delle voci DNS può richiedere fino a 48 ore, a volte più. Se tutto è stato configurato correttamente, è necessario attendere che la propagazione abbia esito positivo.
> 
> 

## Passaggi successivi
Informazioni su come proteggere il nome di dominio personalizzato tramite HTTPS [acquistando un certificato SSL in Azure](web-sites-purchase-ssl-web-site.md) o [usando un certificato SSL per un dominio personalizzato](web-sites-configure-ssl-certificate.md).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

[Introduzione al servizio DNS di Azure](../dns/dns-getstarted-create-dnszone.md) [Creare record DNS per un'app Web in un dominio personalizzato](../dns/dns-web-sites-custom-domain.md) [Delegare un dominio al servizio DNS di Azure](../dns/dns-domain-delegation.md)

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0928_2016-->