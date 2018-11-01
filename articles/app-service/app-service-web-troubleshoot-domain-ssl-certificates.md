---
title: Risolvere i problemi relativi al dominio e al certificato SSL nelle app Web di Azure | Microsoft Docs
description: Risolvere i problemi relativi al dominio e al certificato SSL nelle app Web di Azure
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e10c6580c29784f8d56a31e267c47ec08894113b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418365"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Risolvere i problemi relativi al dominio e al certificato SSL nelle app Web di Azure

Questo articolo elenca i problemi comuni riscontrabili quando si configura un dominio o un certificato SSL per le app Web di Azure. L'articolo descrive anche le possibili cause e soluzioni per tali problemi.

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiesta di supporto**.

## <a name="certificate-problems"></a>Problemi certificati

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Non è consentito aggiungere un'associazione di certificato SSL a un'app Web 

#### <a name="symptom"></a>Sintomo

Quando si aggiunge un'associazione SSL, si riceve il messaggio di errore seguente:

"Non è stato possibile aggiungere l'associazione SSL. Impossibile impostare il certificato per l'indirizzo VIP esistente perché tale certificato è già utilizzato da un altro indirizzo VIP."

#### <a name="cause"></a>Causa

Questo problema può verificarsi se si dispone di più associazioni SSL basate su IP per lo stesso indirizzo IP tra più app Web. Ad esempio, l'app Web A dispone di un SSL basato su IP con un certificato precedente. L'app Web B ha un SSL basato su IP con un nuovo certificato per lo stesso indirizzo IP. Quando si aggiorna l'associazione SSL dell'app Web con il nuovo certificato, l'operazione ha esito negativo con questo errore, in quanto lo stesso indirizzo IP è in uso per un'altra app. 

#### <a name="solution"></a>Soluzione 

Per risolvere il problema, usare uno dei metodi seguenti:

- Eliminare l'associazione SSL basata su IP nell'app Web che usa il certificato precedente. 
- Creare una nuova associazione SSL basata su IP che usi il nuovo certificato.

### <a name="you-cant-delete-a-certificate"></a>Non è possibile eliminare un certificato 

#### <a name="symptom"></a>Sintomo

Quando si tenta di eliminare un certificato, viene visualizzato il messaggio di errore seguente:

"Impossibile eliminare il certificato perché è attualmente in uso in un'associazione SSL. Prima di poter eliminare il certificato, è necessario rimuovere l'associazione SSL."

#### <a name="cause"></a>Causa

Questo problema può verificarsi se il certificato è usato da un'altra app Web.

#### <a name="solution"></a>Soluzione

Rimuovere l'associazione SSL per il certificato dalle app Web, quindi provare a eliminare il certificato. Se non è ancora possibile eliminare il certificato, cancellare la cache del browser Internet e riaprire il portale di Azure in una nuova finestra del browser. quindi provare a eliminare il certificato.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Non è possibile acquistare un certificato del servizio app 

#### <a name="symptom"></a>Sintomo
Non è possibile acquistare un [certificato del servizio app di Azure](./web-sites-purchase-ssl-web-site.md) dal portale di Azure.

#### <a name="cause-and-solution"></a>Causa e soluzione
Questo problema può verificarsi per uno dei motivi seguenti:

- Il piano di servizio app è Gratuito o Condiviso. Questi livelli di prezzo non supportano SSL. 

    **Soluzione**: aggiornare il piano di servizio app per l'app Web a Standard.

- La sottoscrizione non dispone di una carta di credito valida.

    **Soluzione**: aggiungere una carta di credito valida per la sottoscrizione. 

- L'offerta di sottoscrizione non consente di acquistare un certificato di servizio app, ad esempio Microsoft Student.  

    **Soluzione**: aggiornare la sottoscrizione. 

- La sottoscrizione ha raggiunto il limite di acquisti consentiti.

    **Soluzione**: i certificati di servizio app prevedono un limite di 10 acquisti di certificato per i tipi di sottoscrizioni con pagamento in base al consumo ed EA. Per gli altri tipi di sottoscrizioni, il limite è 3. Per aumentare il limite, contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Il certificato del servizio app è stato contrassegnato come illecito. È stato visualizzato il messaggio di errore seguente: "Il certificato è stato segnalato per una potenziale frode. È in corso l'esame della richiesta. Se il certificato non diventa utilizzabile entro 24 ore, contattare il supporto tecnico di Azure".

    **Soluzione**: se il certificato è contrassegnato come illecito e il problema non viene risolto entro 24 ore, attenersi alla procedura seguente:

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Passare a **Certificati del servizio app** e selezionare il certificato.
    3. Selezionare **Configurazione del certificato** > **Passaggio 2: Verifica** > **Verifica del dominio**. Questo passaggio invia una notifica di posta elettronica al provider del certificato di Azure per risolvere il problema.

## <a name="domain-problems"></a>Problemi di dominio

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>È stato acquistato un certificato SSL per il dominio errato

#### <a name="symptom"></a>Sintomo

È stato acquistato un certificato del servizio app per il dominio errato. Non è possibile aggiornare il certificato per usare il dominio corretto.

#### <a name="solution"></a>Soluzione

Eliminare il certificato e quindi acquistare un nuovo certificato.

Se il certificato corrente che utilizza il dominio non corretto è nello stato "Rilasciato", si riceverà un addebito anche per tale certificato. I certificati del servizio app non sono rimborsabili, ma è possibile contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per verificare se siano disponibili altre opzioni. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Un certificato del servizio app è stato rinnovato ma nell'app Web viene mostrato il certificato precedente 

#### <a name="symptom"></a>Sintomo

Il certificato del servizio app è stato rinnovato, ma l'app Web in questione usa ancora il certificato precedente. Inoltre, è stato ricevuto un avviso che indica che è necessario il protocollo HTTPS.

#### <a name="cause"></a>Causa 
La funzione app Web del servizio app di Azure esegue un processo in background ogni otto ore e sincronizza la risorsa del certificato in caso di modifiche. Quando si ruota o si aggiorna un certificato, in alcuni casi l'applicazione recupera ancora il certificato precedente e non il certificato appena aggiornato. Questo avviene perché il processo di sincronizzazione della risorsa certificato non è stato ancora eseguito. 
 
#### <a name="solution"></a>Soluzione

È possibile forzare una sincronizzazione del certificato:

1. Accedere al [portale di Azure](https://portal.azure.com). Passare a **Certificati del servizio app** e selezionare il certificato.
2. Selezionare **Reimposta chiavi e sincronizza**, quindi selezionare **Sincronizza**. La sincronizzazione richiede tempo. 
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

Ad esempio, se si acquista un certificato standard per azure.com con il token di verifica del dominio 1234abcd, una richiesta Web inviata a http://azure.com/1234abcd.html dovrebbe restituire 1234abcd. 

> [!IMPORTANT]
> Un ordine di certificato dispone di soli 15 giorni per completare l'operazione di verifica del dominio. Dopo 15 giorni il certificato viene negato dall'autorità di certificazione e non vengono addebitate le spese per il certificato. In questo caso, eliminare il certificato e riprovare.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Non è possibile acquistare un dominio

#### <a name="symptom"></a>Sintomo
Non è possibile acquistare un dominio dalle app Web o dal dominio del servizio app nel portale di Azure.

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

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Non è possibile aggiungere un nome host a un'app Web 

#### <a name="symptom"></a>Sintomo

Quando si aggiunge un nome host, il processo non riesce a convalidare e verificare il dominio.

#### <a name="cause"></a>Causa 

Questo problema si verifica per uno dei motivi seguenti:

- Non si è autorizzati ad aggiungere un nome host.

    **Soluzione**: chiedere all'amministratore della sottoscrizione di concedere l'autorizzazione per aggiungere un nome host.
- Non è stato possibile verificare la proprietà del dominio.

    **Soluzione**: verificare che il record CNAME o il record A sia configurato correttamente. Per eseguire il mapping di un dominio personalizzato all'app Web, creare un record CNAME o un record A. Se si desidera usare un dominio radice, è necessario usare i record A e TXT:

    |Tipo di record|Host|Punta a|
    |------|------|-----|
    |Una |@|Indirizzo IP per un'app Web|
    |TXT|@|<nome-app>.azurewebsites.net|
    |CNAME|www|<nome-app>.azurewebsites.net|

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
- Usare [WhatsmyDNS.net](https://www.whatsmydns.net/) per verificare che il dominio punti all'indirizzo IP dell'app Web. In caso contrario, configurare il record A con l'indirizzo IP corretto dell'app Web.

### <a name="you-need-to-restore-a-deleted-domain"></a>È necessario ripristinare un dominio eliminato 

#### <a name="symptom"></a>Sintomo
Il dominio non è più visibile nel portale di Azure.

#### <a name="cause"></a>Causa 
Il dominio potrebbe essere stato eliminato per sbaglio dal proprietario della sottoscrizione.

#### <a name="solution"></a>Soluzione
Se il dominio è stato eliminato da meno di sette giorni, il processo di eliminazione non sarà ancora stato avviato. In questo caso, è possibile acquistare nuovamente lo stesso dominio nel portale di Azure nella stessa sottoscrizione. Assicurarsi di digitare il nome di dominio esatto nella casella di ricerca. Non verrà applicato alcun nuovo addebito per questo dominio. Se il dominio è stato eliminato da più di sette giorni, contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per istruzioni su come ripristinare il dominio.

### <a name="a-custom-domain-returns-a-404-error"></a>Un dominio personalizzato restituisce un errore 404 

#### <a name="symptom"></a>Sintomo

Quando si passa al sito usando il nome di dominio personalizzato, viene visualizzato il messaggio di errore seguente:

"Error 404-Web app not found." (Errore 404 - App Web non trovata.)


#### <a name="cause-and-solution"></a>Causa e soluzione

**Causa 1** 

Nel dominio personalizzato configurato mancano un record CNAME o un record A. 

**Soluzione per la causa 1**

- Se è stato aggiunto un record A, assicurarsi che sia stato aggiunto anche un record TXT. Per altre informazioni, vedere [Creazione di un record A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se non è necessario usare il dominio radice per l'app Web, è consigliabile usare un record CNAME anziché un record A.
- Non usare sia un record CNAME sia un record A per lo stesso dominio. Ciò può provocare un conflitto e impedire la risoluzione del dominio. 

**Causa 2** 

Nella cache del browser Internet potrebbe ancora essere memorizzato l'indirizzo IP precedente del dominio. 

**Soluzione per la causa 2**

Eliminare i dati del browser. Per i dispositivi Windows, è possibile eseguire il comando `ipconfig /flushdns`. Usare [WhatsmyDNS.net](https://www.whatsmydns.net/) per verificare che il dominio punti all'indirizzo IP dell'app Web. 

### <a name="you-cant-add-a-subdomain"></a>Non è possibile aggiungere un sottodominio 

#### <a name="symptom"></a>Sintomo

Non è possibile aggiungere un nuovo nome host a un'app Web per assegnare un sottodominio.

#### <a name="solution"></a>Soluzione

- Rivolgersi all'amministratore della sottoscrizione per assicurarsi di avere l'autorizzazione ad aggiungere un nome host all'app Web.
- Se sono necessari più sottodomini, si consiglia di modificare l'hosting del dominio in DNS di Azure. Usando DNS Azure, è possibile aggiungere 500 nomi host all'app Web. Per altre informazioni, vedere [Mapping a custom subdomain to an Azure Website](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/) (Mapping di un sottodominio personalizzato a un sito Web Azure).











 


















