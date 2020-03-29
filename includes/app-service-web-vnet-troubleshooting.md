---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671518"
---
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. È anche possibile raggiungere la console di Kudo all'indirizzo [sitename].scm.azurewebsites.net.You can also reach the Kudo console at [sitename].scm.azurewebsites.net. Una volta caricato il sito Web, passare alla scheda della console di debug. Per accedere alla console ospitata del portale di Azure, passare dall'app a Strumenti -> console. 

#### <a name="tools"></a>Strumenti
Il **ping**degli strumenti , **nslookup**e **tracert** non funzioneranno attraverso la console a causa di vincoli di sicurezza. Per riempire il vuoto, sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

Puoi usare **nameresolver** per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS. È possibile visualizzare il server DNS che verrà utilizzato dall'app nella console esaminando le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere un host e una porta specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Utilizzare lo strumento **tcpping** per verificare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**. Il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale. Se non è accessibile, si potrebbe avere un firewall o NSG bloccando l'accesso al DNS o potrebbe essere inattivo.

Se questi elementi non rispondono ai tuoi problemi, cerca prima cose come: 

**Integrazione di reti virtuali regionali**
* destinazione è un indirizzo non RFC1918 e non è WEBSITE_VNET_ROUTE_ALL impostato su 1?
* esiste un gruppo di sicurezza di rete che blocca l'uscita dalla subnet di integrazione?
* Se si passa attraverso ExpressRoute o una VPN, il gateway locale è configurato per instradare il traffico in Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, controllare le route.
* si dispone di autorizzazioni sufficienti per impostare la delega nella subnet di integrazione? Durante la configurazione dell'integrazione della rete virtuale regionale, la subnet di integrazione verrà delegata a Microsoft.Web.During regional VNet Integration configuration, your integration subnet will be delegated to Microsoft.Web. L'interfaccia utente di integrazione della rete virtuale delecherà automaticamente la subnet a Microsoft.Web.The VNet Integration UI will delegate the subnet to Microsoft.Web automatically. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare la delega, sarà necessario un utente che possa impostare gli attributi nella subnet di integrazione per delegare la subnet. To manually delegate the integration subnet, go to the Azure Virtual Network subnet UI and set delegation for Microsoft.Web. 

**Integrazione della rete virtuale richiesta dal gateway**
* è l'intervallo di indirizzi da punto a sito negli intervalli RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si sospetta che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si sospetta che sia stata apportata una modifica alla configurazione della rete virtuale che non è stata sincronizzata con gli ASP, premi "Sincronizza rete".
* Se si passa attraverso una VPN, il gateway locale è configurato per instradare il traffico in Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, controllare le route.
* Si sta tentando di usare un gateway di coesistenza che supporta sia il punto di punto che ExpressRoute? I gateway di coesistenza non sono supportati con l'integrazione della rete virtuale.

Il debug dei problemi di rete è una sfida perché non è possibile vedere cosa blocca l'accesso a una combinazione host:porta specifica. Tra le cause possibili:

* Si dispone di un firewall nell'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP del punto all'intervallo IP del sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattivo.
* Avevi l'IP o il nome host sbagliato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint. 
* I gruppi di sicurezza di rete sono configurati in modo tale da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a quello del sito.

Ricorda che non sai quale indirizzo verrà effettivamente usato dall'app. Potrebbe essere qualsiasi indirizzo nella subnet di integrazione o nell'intervallo di indirizzi da punto a sito, pertanto è necessario consentire l'accesso dall'intero intervallo di indirizzi. 

Di seguito è riportata la procedura di debug aggiuntiva:

* Connettersi a una macchina virtuale nella rete virtuale e tentare di raggiungere l'host delle risorse: porta da lì. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* Visualizzare un'applicazione in una macchina virtuale e testare l'accesso a tale host e porta dalla console dall'app **usando il tcpping**

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla propria rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, la connessione VPN o ExpressRoute potrebbe non essere configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* Le route non sono configurate con la subnet o puntano a intervalli di indirizzi del sito nel gateway locale.
* I gruppi di sicurezza di rete bloccano l'accesso per l'intervallo IP da punto a sito.
* I firewall locali bloccano il traffico dall'intervallo IP da punto a sito.
* Si sta tentando di raggiungere un indirizzo non RFC 1918 utilizzando la funzionalità di integrazione della rete virtuale regionale.