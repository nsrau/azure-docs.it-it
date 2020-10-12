---
title: Hosting di più siti in un gateway applicazione di Azure
description: Questo articolo offre una panoramica del supporto di più siti in un gateway applicazione di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474326"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hosting di più siti in un gateway applicazione

L'hosting di più siti consente di configurare più di un'applicazione Web sulla stessa porta di un gateway applicazione. Consente di configurare una topologia più efficiente per le distribuzioni aggiungendo fino a più di 100 siti Web a un unico gateway applicazione. Ogni sito Web può essere indirizzato al proprio pool back-end. Ad esempio, tre domini, contoso.com, fabrikam.com e adatum.com, puntano all'indirizzo IP del gateway applicazione. Si creeranno tre listener multisito e si configurerà ogni listener per la rispettiva impostazione della porta e del protocollo. 

È anche possibile definire nomi host con caratteri jolly in un listener multisito e fino a cinque nomi host per ogni listener. Per altre informazioni, vedere [nomi host con caratteri jolly nel listener](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Gateway applicazione multisito":::

> [!IMPORTANT]
> Le regole vengono elaborate nell'ordine in cui sono elencate nel portale per lo SKU V1. Per lo SKU V2, le corrispondenze esatte hanno precedenza superiore. È consigliabile configurare i listener multisito prima di configurare un listener di base.  In questo modo il traffico viene indirizzato al back-end appropriato. Se un listener di base viene elencato per primo e corrisponde a una richiesta in ingresso, sarà tale listener a elaborarla.

Per le richieste `http://contoso.com` viene eseguito il routing verso ContosoServerPool mentre per le richieste `http://fabrikam.com` viene eseguito il routing verso FabrikamServerPool.

Analogamente, è possibile ospitare più sottodomini dello stesso dominio padre nella stessa distribuzione del gateway applicazione. Ad esempio, è possibile ospitare `http://blog.contoso.com` e `http://app.contoso.com` in una singola distribuzione del gateway applicazione.

## <a name="wildcard-host-names-in-listener-preview"></a>Nomi host con caratteri jolly nel listener (anteprima)

Il gateway applicazione consente il routing basato su host mediante listener HTTP (S) multisito. Ora è possibile usare caratteri jolly come asterisco (*) e punto interrogativo (?) nel nome host e fino a 5 nomi host per listener HTTP (S) multisito. Ad esempio: `*.contoso.com`.

Utilizzando un carattere jolly nel nome host, è possibile associare più nomi host in un singolo listener. Ad esempio, `*.contoso.com` può corrispondere a `ecom.contoso.com` , così `b2b.contoso.com` come `customer1.b2b.contoso.com` e così via. Utilizzando una matrice di nomi host, è possibile configurare più di un nome host per un listener per indirizzare le richieste a un pool back-end. Un listener può ad esempio contenere, `contoso.com, fabrikam.com` che accetterà le richieste per entrambi i nomi host.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Gateway applicazione multisito":::

>[!NOTE]
> Questa funzionalità è disponibile in anteprima ed è disponibile solo per Standard_v2 e WAF_v2 SKU del gateway applicazione. Per ulteriori informazioni sulle anteprime, vedere [le condizioni per l'utilizzo qui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>Questa funzionalità è attualmente disponibile solo tramite [Azure PowerShell](tutorial-multiple-sites-powershell.md) e l'interfaccia della riga di comando di [Azure](tutorial-multiple-sites-cli.md). Il supporto del portale sarà presto disponibile.
> Si noti che poiché il supporto del portale non è completamente disponibile, se si usa solo il parametro HostNames, il listener sarà visualizzato come listener di base nel portale e nella colonna nome host della visualizzazione elenco listener non verranno visualizzati i nomi host configurati. Per tutte le modifiche apportate a un listener con caratteri jolly, assicurarsi di usare Azure PowerShell o CLI fino a quando non è supportato nel portale.

In [Azure PowerShell](tutorial-multiple-sites-powershell.md), è necessario usare `-HostNames` anziché `-HostName` . Con i nomi host, è possibile menzionare fino a 5 nomi host come valori delimitati da virgole e usare caratteri jolly. Ad esempio: `-HostNames "*.contoso.com,*.fabrikam.com"`

Nell' [interfaccia](tutorial-multiple-sites-cli.md)della riga di comando di Azure è necessario usare `--host-names` anziché `--host-name` . Con i nomi host, è possibile menzionare fino a 5 nomi host come valori delimitati da virgole e usare caratteri jolly. Ad esempio: `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Caratteri consentiti nel campo nomi host:

* `(A-Z,a-z,0-9)` -caratteri alfanumerici
* `-` -trattino o meno
* `.` -periodo come delimitatore
*   `*` -può corrispondere a più caratteri nell'intervallo consentito
*   `?` -può corrispondere a un singolo carattere nell'intervallo consentito

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Condizioni per l'utilizzo di caratteri jolly e più nomi host in un listener:

*   È possibile menzionare fino a 5 nomi host in un singolo listener
*   L'asterisco `*` può essere indicato una sola volta in un componente di un nome di stile del dominio o di un nome host. Ad esempio, Component1 *. Component2*. Component3. `(*.contoso-*.com)` è valido.
*   In un nome host possono essere presenti solo due asterischi `*` . Ad esempio, `*.contoso.*` è valido e `*.contoso.*.*.com` non è valido.
*   Il nome host può contenere un massimo di 4 caratteri jolly. Ad esempio, `????.contoso.com` , `w??.contoso*.edu.*` sono validi, ma `????.contoso.*` non sono validi.
*   L'utilizzo di un asterisco e di un `*` punto interrogativo `?` insieme in un componente di un nome host ( `*?` o `?*` o `**` ) non è valido. Ad esempio, `*?.contoso.com` e `**.contoso.com` non sono validi.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Considerazioni e limitazioni relative all'utilizzo di caratteri jolly o più nomi host in un listener:

*   Per la [terminazione SSL e l'SSL end-to-end](ssl-overview.md) è necessario configurare il protocollo come HTTPS e caricare un certificato da usare nella configurazione del listener. Se si tratta di un listener multisito, è possibile immettere anche il nome host, in genere il valore CN del certificato SSL. Quando si specificano più nomi host nel listener o si utilizzano caratteri jolly, è necessario considerare quanto segue:
    *   Se si tratta di un nome host con caratteri jolly, ad esempio *. contoso.com, è necessario caricare un certificato con caratteri jolly con CN like *. contoso.com
    *   Se nello stesso listener sono citati più nomi host, è necessario caricare un certificato SAN (nome alternativo del soggetto) con il sistema nervoso che corrisponde ai nomi host indicati.
*   Non è possibile usare un'espressione regolare per citare il nome host. Per creare il modello di nome host, è possibile usare solo caratteri jolly, ad esempio asterisco (*) e punto interrogativo (?).
*   Per il controllo di integrità back-end, non è possibile associare più [Probe personalizzati](application-gateway-probe-overview.md) per ogni impostazione http. È invece possibile analizzare uno dei siti Web nel back-end o usare "127.0.0.1" per verificare l'localhost del server back-end. Tuttavia, quando si usa un carattere jolly o più nomi host in un listener, le richieste per tutti i modelli di dominio specificati verranno indirizzate al pool back-end a seconda del tipo di regola (di base o basato sul percorso).
*   Le proprietà "hostname" accetta una stringa come input, in cui è possibile menzionare solo un nome di dominio non con caratteri jolly e "hostnames" accetta una matrice di stringhe come input, in cui è possibile menzionare fino a 5 nomi di dominio con caratteri jolly. Tuttavia, entrambe le proprietà non possono essere usate contemporaneamente.
*   Non è possibile creare una regola di [Reindirizzamento](redirect-overview.md) con un listener di destinazione che usa caratteri jolly o più nomi host.

Vedere [creare più siti usando Azure PowerShell](tutorial-multiple-sites-powershell.md) o l' [interfaccia](tutorial-multiple-sites-cli.md) della riga di comando di Azure per la guida dettagliata su come configurare i nomi host con caratteri jolly in un listener multisito.

## <a name="host-headers-and-server-name-indication-sni"></a>Intestazioni host e indicazione nome server (SNI)

Per abilitare l'hosting di più siti nella stessa infrastruttura sono disponibili tre comuni meccanismi.

1. Ospitare più applicazioni Web con un indirizzo IP univoco per ognuna.
2. Usare il nome host per ospitare più applicazioni Web nello stesso indirizzo IP.
3. Usare porte diverse per ospitare più applicazioni Web nello stesso indirizzo IP.

Il gateway applicazione attualmente supporta un solo indirizzo IP pubblico in cui è in ascolto del traffico. Quindi, più applicazioni, ognuna con un proprio indirizzo IP non è attualmente supportata. 

Il gateway applicazione supporta più applicazioni ognuna in ascolto su porte diverse, ma questo scenario richiede che le applicazioni accettino il traffico sulle porte non standard. Spesso non si tratta di una configurazione desiderata.

Il gateway applicazione si basa su intestazioni host HTTP 1.1 per ospitare più siti Web nello stesso indirizzo IP pubblico e nella stessa porta. I siti ospitati nel gateway applicazione possono anche supportare l'offload TLS con Indicazione nome server (SNI) estensione TLS. In questo scenario il browser client e la Web farm back-end devono quindi supportare HTTP/1.1 e l'estensione TLS definita nella specifica RFC 6066.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare l'hosting di più siti nel gateway applicazione
* [Uso del portale di Azure](create-multiple-sites-portal.md)
* [Uso di Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [Utilizzare l'interfaccia della riga di comando di Azure](tutorial-multiple-sites-cli.md)

Per una distribuzione basata su modello end-to-end, vedere il [modello di Resource Manager con hosting di più siti](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting).
