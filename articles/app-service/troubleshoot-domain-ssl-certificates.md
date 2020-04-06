---
title: Risolvere i problemi relativi ai certificati di dominio e TLS/SSLTroubleshoot domain and TLS/SSL certificates
description: Trovare soluzioni ai problemi comuni che possono verificarsi quando si configura un dominio o un certificato TLS/SSL nel servizio app di Azure.Find solutions to the common problems that you might encounter when you configure a domain or TLS/SSL certificate in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: d61b95c7136a4cbce11789a58d27cc1a164ae374
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668031"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Risolvere i problemi relativi al dominio e ai certificati TLS/SSL nel servizio app di AzureTroubleshoot domain and TLS/SSL certificate problems in Azure App Service

Questo articolo elenca i problemi comuni che possono verificarsi quando si configura un dominio o un certificato TLS/SSL per le app Web nel servizio app di Azure.This article lists common problems that you might encounter when you configure a domain or TLS/SSL certificate for your web apps in Azure App Service. L'articolo descrive anche le possibili cause e soluzioni per tali problemi.

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni **supporto**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemi certificati

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Non è possibile aggiungere un binding di certificato TLS/SSL a un'appYou can't add a TLS/SSL certificate binding to an app 

#### <a name="symptom"></a>Sintomo

Quando si aggiunge un'associazione TLS, viene visualizzato il seguente messaggio di errore:

"Non è stato possibile aggiungere l'associazione SSL. Impossibile impostare il certificato per l'indirizzo VIP esistente perché tale certificato è già utilizzato da un altro indirizzo VIP."

#### <a name="cause"></a>Causa

Questo problema può verificarsi se si dispone di più associazioni SSL basate su IP per lo stesso indirizzo IP tra più app. Ad esempio, l'app A dispone di un SSL basato su IP con un certificato precedente. L'app B ha un SSL basato su IP con un nuovo certificato per lo stesso indirizzo IP. Quando si aggiorna l'associazione TLS dell'app con il nuovo certificato, non riesce con questo errore perché lo stesso indirizzo IP viene utilizzato per un'altra app. 

#### <a name="solution"></a>Soluzione 

Per risolvere il problema, usare uno dei metodi seguenti:

- Eliminare l'associazione SSL basata su IP nell'app che usa il certificato precedente. 
- Creare una nuova associazione SSL basata su IP che usi il nuovo certificato.

### <a name="you-cant-delete-a-certificate"></a>Non è possibile eliminare un certificato 

#### <a name="symptom"></a>Sintomo

Quando si tenta di eliminare un certificato, viene visualizzato il messaggio di errore seguente:

"Impossibile eliminare il certificato perché è attualmente in uso in un binding TLS/SSL. L'associazione TLS deve essere rimossa prima di poter eliminare il certificato."

#### <a name="cause"></a>Causa

Questo problema può verificarsi se il certificato è usato da un'altra app.

#### <a name="solution"></a>Soluzione

Rimuovere l'associazione TLS per il certificato dalle app. quindi provare a eliminare il certificato. Se non è ancora possibile eliminare il certificato, cancellare la cache del browser Internet e riaprire il portale di Azure in una nuova finestra del browser. quindi provare a eliminare il certificato.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Non è possibile acquistare un certificato del servizio app 

#### <a name="symptom"></a>Sintomo
Non è possibile acquistare un [certificato del servizio app di Azure](./configure-ssl-certificate.md#import-an-app-service-certificate) dal portale di Azure.

#### <a name="cause-and-solution"></a>Causa e soluzione
Questo problema può verificarsi per uno dei motivi seguenti:

- Il piano di servizio app è Gratuito o Condiviso. Questi piani tariffari non supportano TLS. 

    **Soluzione:** aggiornare il piano di servizio app per l'app a Standard.

- La sottoscrizione non dispone di una carta di credito valida.

    **Soluzione**: aggiungere una carta di credito valida per la sottoscrizione. 

- L'offerta di sottoscrizione non consente di acquistare un certificato di servizio app, ad esempio Microsoft Student.  

    **Soluzione**: aggiornare la sottoscrizione. 

- La sottoscrizione ha raggiunto il limite di acquisti consentiti.

    **Soluzione**: i certificati di servizio app prevedono un limite di 10 acquisti di certificato per i tipi di sottoscrizioni con pagamento in base al consumo ed EA. Per gli altri tipi di sottoscrizioni, il limite è 3. Per aumentare il limite, contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Il certificato del servizio app è stato contrassegnato come illecito. È stato visualizzato il messaggio di errore seguente: "Il certificato è stato segnalato per una potenziale frode. È in corso l'esame della richiesta. Se il certificato non diventa utilizzabile entro 24 ore, contattare il supporto di Azure."

    **Soluzione**: se il certificato è contrassegnato come illecito e il problema non viene risolto entro 24 ore, attenersi alla procedura seguente:

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Passare a **Certificati del servizio app** e selezionare il certificato.
    3. Selezionare **Configurazione** > certificato**Passaggio 2: Verifica** > **verifica dominio**. Questo passaggio invia una notifica di posta elettronica al provider del certificato di Azure per risolvere il problema.

## <a name="custom-domain-problems"></a>Problemi relativi al dominio personalizzato

### <a name="a-custom-domain-returns-a-404-error"></a>Un dominio personalizzato restituisce un errore 404 

#### <a name="symptom"></a>Sintomo

Quando si passa al sito usando il nome di dominio personalizzato, viene visualizzato il messaggio di errore seguente:

"Error 404-Web app not found." (Errore 404 - App Web non trovata.)

#### <a name="cause-and-solution"></a>Causa e soluzione

**Causa 1** 

Nel dominio personalizzato configurato mancano un record CNAME o un record A. 

**Soluzione per la causa 1**

- Se è stato aggiunto un record A, assicurarsi che sia stato aggiunto anche un record TXT. Per altre informazioni, vedere [Creazione di un record A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se non è necessario usare il dominio radice per l'app, è consigliabile usare un record CNAME anziché un record A.
- Non usare sia un record CNAME sia un record A per lo stesso dominio. Questo problema può causare un conflitto e impedire la risoluzione del dominio. 

**Causa 2** 

Nella cache del browser Internet potrebbe ancora essere memorizzato l'indirizzo IP precedente del dominio. 

**Soluzione per la causa 2**

Eliminare i dati del browser. Per i dispositivi Windows, è possibile eseguire il comando `ipconfig /flushdns`. Usare [WhatsmyDNS.net](https://www.whatsmydns.net/) per verificare che il dominio punti all'indirizzo IP dell'app. 

### <a name="you-cant-add-a-subdomain"></a>Non è possibile aggiungere un sottodominio 

#### <a name="symptom"></a>Sintomo

Non è possibile aggiungere un nuovo nome host a un'app per assegnare un sottodominio.

#### <a name="solution"></a>Soluzione

- Rivolgersi all'amministratore della sottoscrizione per assicurarsi di avere l'autorizzazione ad aggiungere un nome host all'app.
- Se sono necessari più sottodomini, è consigliabile modificare l'hosting del dominio in DNS (Domain Name Service) di Azure. Usando DNS Azure, è possibile aggiungere 500 nomi host all'app. Per altre informazioni, vedere [Mapping a custom subdomain to an Azure Website](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/) (Mapping di un sottodominio personalizzato a un sito Web Azure).

### <a name="dns-cant-be-resolved"></a>Impossibile risolvere il DNS

#### <a name="symptom"></a>Sintomo

È stato visualizzato il messaggio di errore seguente:

"Non è possibile trovare il record DNS."

#### <a name="cause"></a>Causa
Questo problema si verifica per uno dei motivi seguenti:

- La durata (TTL) non è trascorsa. Controllare la configurazione DNS per il dominio per determinare il valore di durata (TTL) e quindi attendere che trascorra tale durata.
- La configurazione DNS non è corretta.

#### <a name="solution"></a>Soluzione
- Attendere 48 ore affinché il problema si risolva da solo.
- Se è possibile modificare l'impostazione di durata (TTL) nella configurazione del DNS, modificare il valore su 5 minuti e controllare se il problema si risolve.
- Usare [WhatsmyDNS.net](https://www.whatsmydns.net/) per verificare che il dominio punti all'indirizzo IP dell'app. In caso contrario, configurare il record A con l'indirizzo IP corretto dell'app.

### <a name="you-need-to-restore-a-deleted-domain"></a>È necessario ripristinare un dominio eliminato 

#### <a name="symptom"></a>Sintomo
Il dominio non è più visibile nel portale di Azure.

#### <a name="cause"></a>Causa 
Il dominio potrebbe essere stato eliminato per sbaglio dal proprietario della sottoscrizione.

#### <a name="solution"></a>Soluzione
Se il dominio è stato eliminato da meno di sette giorni, il processo di eliminazione non sarà ancora stato avviato. In questo caso, è possibile acquistare nuovamente lo stesso dominio nel portale di Azure nella stessa sottoscrizione. (Assicurarsi di digitare il nome di dominio esatto nella casella di ricerca.) Non ti verranno addebitati più addebiti per questo dominio. Se il dominio è stato eliminato più di sette giorni fa, contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per assistenza sul ripristino del dominio.

## <a name="domain-problems"></a>Problemi di dominio

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>È stato acquistato un certificato TLS/SSL per il dominio errato

#### <a name="symptom"></a>Sintomo

È stato acquistato un certificato del servizio app per il dominio errato. Non è possibile aggiornare il certificato per usare il dominio corretto.

#### <a name="solution"></a>Soluzione

Eliminare il certificato e quindi acquistare un nuovo certificato.

Se il certificato corrente che utilizza il dominio non corretto è nello stato "Rilasciato", si riceverà un addebito anche per tale certificato. I certificati del servizio app non sono rimborsabili, ma è possibile contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per verificare se siano disponibili altre opzioni. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Un certificato del servizio app è stato rinnovato ma nell'app viene mostrato il certificato precedente 

#### <a name="symptom"></a>Sintomo

Il certificato del servizio app è stato rinnovato, ma l'app in questione usa ancora il certificato precedente. Inoltre, è stato ricevuto un avviso che indica che è necessario il protocollo HTTPS.

#### <a name="cause"></a>Causa 
Il servizio app sincronizza automaticamente il certificato entro 48 ore. Quando si ruota o si aggiorna un certificato, in alcuni casi l'applicazione recupera ancora il certificato precedente e non il certificato appena aggiornato. Questo avviene perché il processo di sincronizzazione della risorsa certificato non è stato ancora eseguito. Fare clic su Sincronizza. L'operazione di sincronizzazione aggiorna automaticamente i binding del nome host per il certificato nel servizio app senza causare tempi di inattività alle app.
 
#### <a name="solution"></a>Soluzione

È possibile forzare una sincronizzazione del certificato:

1. Accedere al [portale di Azure](https://portal.azure.com). Passare a **Certificati del servizio app** e selezionare il certificato.
2. Selezionare **Rekey and Sync**, quindi **Selezionare Sync**. Il completamento della sincronizzazione richiede del tempo. 
3. Al termine della sincronizzazione, viene visualizzata la notifica seguente: "Aggiornate correttamente tutte le risorse con l'ultimo certificato".

### <a name="domain-verification-is-not-working"></a>La verifica del dominio non funziona 

#### <a name="symptom"></a>Sintomo 
Il certificato del servizio app richiede la verifica del dominio prima di essere pronto per l'uso. Quando si seleziona **Verifica**, il processo ha esito negativo.

#### <a name="solution"></a>Soluzione
Verificare manualmente il dominio aggiungendo un record TXT:
 
1.  Passare al provider DNS (Domain Name Service) che ospita il nome di dominio.
2.  Aggiungere un record TXT per il dominio che usa il valore del token di dominio visualizzato nel portale di Azure. 

Attendere alcuni minuti per l'esecuzione della propagazione del DNS e quindi selezionare il pulsante **Aggiorna** per attivare la verifica. 

In alternativa, è possibile utilizzare il metodo Pagina Web HTML per verificare manualmente il dominio. Questo metodo consente all'autorità di certificazione di confermare la proprietà del dominio per cui è stato rilasciato il certificato.

1.  Creare un file HTML denominato {domain verification token}.html. Il contenuto di questo file deve corrispondere al valore del token di verifica del dominio.
3.  Caricare il file nella radice del server Web che ospita il dominio.
4.  Selezionare **Aggiorna** per controllare lo stato del certificato. Il completamento della verifica potrebbe richiedere qualche minuto.

Ad esempio, se si acquista un certificato standard per azure.com con il token di verifica del dominio 1234abcd, una richiesta Web inviata a https://azure.com/1234abcd.html dovrebbe restituire 1234abcd. 

> [!IMPORTANT]
> Un ordine di certificato dispone di soli 15 giorni per completare l'operazione di verifica del dominio. Dopo 15 giorni il certificato viene negato dall'autorità di certificazione e non vengono addebitate le spese per il certificato. In questo caso, eliminare il certificato e riprovare.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Non è possibile acquistare un dominio

#### <a name="symptom"></a>Sintomo
Non è possibile acquistare un dominio del servizio app nel portale di Azure.

#### <a name="cause-and-solution"></a>Causa e soluzione

Questo problema si verifica per uno dei motivi seguenti:

- Nessuna carta di credito nella sottoscrizione di Azure oppure la carta di credito non è valida.

    **Soluzione**: aggiungere una carta di credito valida per la sottoscrizione.

- Chi non è proprietario della sottoscrizione non è autorizzato ad acquistare un dominio.

    **Soluzione**: [assegnare il ruolo Proprietario](../role-based-access-control/role-assignments-portal.md) al proprio account. In alternativa, contattare l'amministratore della sottoscrizione per ottenere l'autorizzazione ad acquistare un dominio.
- È stato raggiunto il limite di acquisto dei domini nella sottoscrizione. Il limite corrente è 20.

    **Soluzione**: per richiedere un aumento del limite, contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Il tipo di sottoscrizione di Azure non supporta l'acquisto di un dominio del servizio app.

    **Soluzione**: aggiornare la sottoscrizione di Azure ad altri tipi di sottoscrizione, ad esempio Pagamento in base a consumo.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Non è possibile aggiungere un nome host a un'app 

#### <a name="symptom"></a>Sintomo

Quando si aggiunge un nome host, il processo non riesce a convalidare e verificare il dominio.

#### <a name="cause"></a>Causa 

Questo problema si verifica per uno dei motivi seguenti:

- Non si è autorizzati ad aggiungere un nome host.

    **Soluzione**: chiedere all'amministratore della sottoscrizione di concedere l'autorizzazione per aggiungere un nome host.
- Non è stato possibile verificare la proprietà del dominio.

    **Soluzione**: verificare che il record CNAME o il record A sia configurato correttamente. Per eseguire il mapping di un dominio personalizzato all'app, creare un record CNAME o un record A. Se si desidera usare un dominio radice, è necessario usare i record A e TXT:

    |Tipo di record|Host|Punta a|
    |------|------|-----|
    |Una|@|Indirizzo IP per l'app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Domande frequenti

**Devo configurare il mio dominio personalizzato per il mio sito web una volta acquistato?**

Quando si acquista un dominio dal portale di Azure, l'applicazione del servizio app viene configurata automaticamente per l'uso di tale dominio personalizzato. Non è necessario eseguire ulteriori passaggi. Per altre informazioni, vedere [Guida autonoma del servizio app di Azure: Aggiungere un nome](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) di dominio personalizzato in Channel9.For more information, watch Azure App Service Self Help: Add a Custom Domain Name on Channel9.

**È possibile usare un dominio acquistato nel portale di Azure per puntare a una macchina virtuale di Azure?**

Sì, è possibile puntare il dominio a una macchina virtuale. Per altre informazioni, vedere [Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure](../dns/dns-custom-domain.md).

**Il dominio è ospitato da GoDaddy o dal DNS di Azure?**

I domini del servizio app usano GoDaddy per la registrazione del dominio e il DNS di Azure per ospitare i domini. 

**Ho abilitato il rinnovo automatico abilitato ma ho ancora ricevuto un avviso di rinnovo per il mio dominio via e-mail. Cosa dovrei fare?**

Se il rinnovo automatico è abilitato, non è necessario eseguire alcuna azione. L'e-mail di avviso viene fornita per informarti che il dominio è vicino alla scadenza e per rinnovare manualmente se il rinnovo automatico non è abilitato.

**Mi verrà addebitato un costo per il DNS di Azure che ospita il mio dominio?**

Il costo iniziale dell'acquisto del dominio si applica solo alla registrazione del dominio. Oltre al costo di registrazione, sono previsti costi per il DNS di Azure in base all'utilizzo. Per altre informazioni, vedere [Prezzi DNS](https://azure.microsoft.com/pricing/details/dns/) di Azure per altri dettagli.

**Il dominio è stato acquistato in precedenza dal portale di Azure e si vuole passare dall'hosting DiODaddy all'hosting DNS di Azure.You purchased my domain earlier from the Azure portal and want to move from GoDaddy hosting to Azure DNS hosting. Come posso farlo?**

Non è obbligatorio eseguire la migrazione all'hosting DNS di Azure.It is not mandatory to migrate to Azure DNS hosting. Se si vuole eseguire la migrazione a DNS di Azure, l'esperienza di gestione del dominio nel portale di Azure in merito ai passaggi necessari per passare al DNS di Azure.If you do do do migrate to Azure DNS, the domain management experience in the Azure portal about provides information on steps necessary to move to Azure DNS. Se il dominio è stato acquistato tramite il servizio app, la migrazione dall'hosting GoDaddy al DNS di Azure è una procedura relativamente semplice.

**Si desidera acquistare il dominio dal dominio del servizio app, ma è possibile ospitare il dominio in GoDaddy anziché nel DNS di Azure?**

A partire dal 24 luglio 2017, i domini del servizio app acquistati nel portale sono ospitati nel DNS di Azure.Start July 24, 2017, App Service domains purchased in the portal are hosted on Azure DNS. Se si preferisce utilizzare un provider di hosting diverso, è necessario visitare il relativo sito Web per ottenere una soluzione di hosting di dominio.

**Devo pagare per la protezione della privacy per il mio dominio?**

Quando si acquista un dominio tramite il portale di Azure, è possibile scegliere di aggiungere la privacy senza costi aggiuntivi. Questo è uno dei vantaggi dell'acquisto del dominio tramite il servizio app di Azure.This is one of the benefits of purchasing your domain through Azure App Service.

**Se decido di non voler più avere il mio dominio, posso riavere i miei soldi?**

Quando acquisti un dominio, non ti viene addebitato un periodo di cinque giorni, durante il quale puoi decidere che non vuoi il dominio. Se decidi di non volere il dominio entro quel periodo di cinque giorni, non ti verrà addebitato alcun costo. (domini .uk sono un'eccezione a questo. Se acquisti un dominio .uk, ti verrà addebitato immediatamente un addebito e non puoi ricevere un rimborso.)

**È possibile usare il dominio in un'altra app del servizio app di Azure nella sottoscrizione?**

Sì. Quando si accede al pannello Domini personalizzati e TLS nel portale di Azure, vengono visualizzati i domini acquistati. Puoi configurare l'app per l'uso di uno di questi domini.

**È possibile trasferire un dominio da una sottoscrizione a un'altra?**

È possibile spostare un dominio in un altro gruppo di sottoscrizioni/risorse usando il cmdlet [PowerShell Move-AzResource.You](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) can move a domain to another subscription/resource group using the Move-AzResource PowerShell cmdlet.

**Come è possibile gestire il dominio personalizzato se attualmente non si dispone di un'app del servizio app di Azure?**

È possibile gestire il dominio anche se non si dispone di un'app Web del servizio app. Il dominio può essere usato per i servizi di Azure come Macchina virtuale, Archiviazione e così via. Se si intende utilizzare il dominio per le app Web del servizio app, è necessario includere un'app Web non inclusa nel piano servizio app gratuito per associare il dominio all'app Web.

**È possibile spostare un'app Web con un dominio personalizzato in un'altra sottoscrizione o da Ambiente servizio app v1 a V2?**

Sì, è possibile spostare l'app Web tra sottoscrizioni. Seguire le indicazioni in [Come spostare le risorse in Azure.Follow](../azure-resource-manager/management/move-resource-group-and-subscription.md)the guidance in How to move resources in Azure. Esistono alcune limitazioni per lo spostamento dell'app Web. Per altre informazioni, vedere Limitazioni per lo spostamento delle [risorse del servizio app.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Dopo aver spostato l'app Web, i binding dei nomi host dei domini all'interno dell'impostazione dei domini personalizzati devono rimanere invariati. Non sono necessari passaggi aggiuntivi per configurare i binding dei nomi host.
