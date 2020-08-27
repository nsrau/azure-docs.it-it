---
title: Configurare un firewall IP per il servizio ricerca cognitiva di Azure
titleSuffix: Azure Cognitive Search
description: Configurare i criteri di controllo IP per limitare l'accesso al servizio ricerca cognitiva di Azure.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3e8a94b6b9b71d2d71b634edd70ea4150652b143
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932798"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Configurare il firewall IP per Azure ricerca cognitiva

Azure ricerca cognitiva supporta le regole IP per il supporto del firewall in ingresso. Questo modello offre un livello aggiuntivo di sicurezza per il servizio di ricerca simile alle regole IP disponibili in un gruppo di sicurezza di rete virtuale di Azure. Con queste regole IP, è possibile configurare il servizio di ricerca in modo che sia accessibile solo da un set approvato di computer e/o servizi cloud. Per accedere ai dati archiviati nel servizio di ricerca da questi set di computer e servizi approvati, sarà comunque necessario che il chiamante presenti un token di autorizzazione valido.

> [!Important]
> Le regole IP nel servizio ricerca cognitiva di Azure possono essere configurate usando il portale di Azure o l' [API REST di gestione versione 2020-03-13](/rest/api/searchmanagement/).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Configurare un firewall IP usando il portale di Azure

Per impostare i criteri di controllo di accesso IP nel portale di Azure, passare alla pagina del servizio ricerca cognitiva di Azure e selezionare **rete** nel menu di navigazione. La connettività di rete di endpoint deve essere **pubblica**. Se la connettività è impostata su **privata**, è possibile accedere al servizio di ricerca solo tramite un endpoint privato.

![Screenshot che illustra come configurare il firewall IP nel portale di Azure](./media/service-configure-firewall/azure-portal-firewall.png)

Il portale di Azure consente di specificare gli indirizzi IP e gli intervalli di indirizzi IP nel formato CIDR. Un esempio di notazione CIDR è 8.8.8.0/24, che rappresenta gli IP che variano da 8.8.8.0 a 8.8.8.255.

> [!NOTE]
> Dopo aver abilitato i criteri di controllo di accesso IP per il servizio Azure ricerca cognitiva, tutte le richieste al piano dati da computer al di fuori dell'elenco degli intervalli di indirizzi IP consentiti vengono rifiutate. Quando vengono configurate regole IP, alcune funzionalità del portale di Azure sono disabilitate. Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale per i dati di indicizzazione e i vari componenti del servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza.

### <a name="requests-from-your-current-ip"></a>Richieste dall'IP corrente

Per semplificare lo sviluppo, il portale di Azure consente di identificare e aggiungere l'indirizzo IP del computer client all'elenco di indirizzi consentiti. Le app in esecuzione nel computer possono accedere al servizio ricerca cognitiva di Azure.

Il portale rileva automaticamente l'indirizzo IP del client. Potrebbe trattarsi dell'indirizzo IP del client del computer o del gateway di rete. Assicurarsi di rimuovere questo indirizzo IP prima di portare il carico di lavoro nell'ambiente di produzione.

Per aggiungere l'indirizzo IP corrente all'elenco di indirizzi IP, selezionare **Aggiungi l'indirizzo IP del client**. Selezionare quindi **Salva**.

![Screenshot che illustra come configurare le impostazioni del firewall IP per consentire l'indirizzo IP corrente](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Risolvere i problemi con un criterio di controllo di accesso IP

È possibile risolvere i problemi con un criterio di controllo accesso IP usando le opzioni seguenti:

### <a name="azure-portal"></a>Portale di Azure

L'abilitazione di un criterio di controllo di accesso IP per il servizio ricerca cognitiva di Azure blocca tutte le richieste provenienti da computer al di fuori dell'elenco di indirizzi IP consentiti, inclusa la portale di Azure.  Sarà possibile visualizzare e gestire le informazioni sul livello di servizio, ma l'accesso al portale per i dati di indicizzazione e i vari componenti del servizio, ad esempio le definizioni di indice, indicizzatore e competenze, è limitato per motivi di sicurezza. 

### <a name="sdks"></a>SDK

Quando si accede al servizio ricerca cognitiva di Azure usando l'SDK da computer che non sono inclusi nell'elenco degli elementi consentiti, viene restituita una risposta generica **403** non consentita senza ulteriori dettagli. Verificare l'elenco di indirizzi IP consentiti per l'account e assicurarsi che sia stata aggiornata la configurazione corretta per il servizio di ricerca.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'accesso al servizio di ricerca tramite collegamento privato, vedere l'articolo seguente:

* [Creare un endpoint privato per una connessione sicura ad Azure ricerca cognitiva](service-create-private-endpoint.md)