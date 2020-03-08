---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671518"
---
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. È anche possibile accedere alla console Kudo in [SiteName]. SCM. azurewebsites. NET. Una volta caricato il sito Web, passare alla scheda Debug Console. Per accedere alla console ospitata portale di Azure quindi dall'app, passare a strumenti-> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup**e **tracert** non funzioneranno tramite la console a causa di vincoli di sicurezza. Per riempire il void, sono stati aggiunti due strumenti distinti. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

È possibile usare **nameresolver** per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS. È possibile visualizzare il server DNS che l'app userà nella console esaminando le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere una porta e un host specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Usare lo strumento **tcpping** per testare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**. Il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale. Se non è accessibile, è possibile che si disponga di un firewall o di un NSG che blocca l'accesso al DNS o che potrebbe essere inattivo.

Se tali elementi non rispondono ai problemi, cercare prima di tutto, ad esempio: 

**Integrazione VNet a livello di area**
* la destinazione è un indirizzo non RFC1918 e non si dispone di WEBSITE_VNET_ROUTE_ALL impostato su 1?
* esiste una NSG che blocca l'uscita dalla subnet di integrazione?
* Se si passa attraverso ExpressRoute o una VPN, il gateway locale è configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet, ma non in locale, verificare le route.
* si dispone di autorizzazioni sufficienti per impostare la delega sulla subnet di integrazione? Durante la configurazione dell'integrazione VNet a livello di area, la subnet di integrazione verrà delegata a Microsoft. Web. L'interfaccia utente di integrazione di VNet delegherà automaticamente la subnet a Microsoft. Web. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare la delega, sarà necessario un utente che può impostare gli attributi nella subnet di integrazione per delegare la subnet. Per delegare manualmente la subnet di integrazione, passare all'interfaccia utente della subnet della rete virtuale di Azure e impostare la delega per Microsoft. Web. 

**Integrazione VNet necessaria per il gateway**
* l'intervallo di indirizzi da punto a sito è compreso negli intervalli RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si ritiene che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si ritiene che sia stata apportata una modifica alla configurazione di VNet che non è stata sincronizzata con gli ASP, fare clic su "Sincronizza rete".
* Se si passa attraverso una VPN, è il gateway locale configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint in VNet, ma non in locale, verificare le route.
* si sta provando a usare un gateway di coesistenza che supporta sia da punto a sito che da ExpressRoute? I gateway di coesistenza non sono supportati con l'integrazione VNet.

Il debug dei problemi di rete è una sfida perché non è possibile vedere che cosa blocca l'accesso a una combinazione host: porta specifica. Tra le cause possibili:

* Nell'host è presente un firewall che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* Il nome host o l'indirizzo IP non è corretto.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint. 
* I gruppi di sicurezza di rete vengono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce l'indirizzo che l'App utilizzerà effettivamente. Potrebbe trattarsi di qualsiasi indirizzo nell'intervallo di indirizzi da punto a sito o subnet di integrazione, quindi è necessario consentire l'accesso dall'intero intervallo di indirizzi. 

Di seguito è riportata la procedura di debug aggiuntiva:

* Connettersi a una macchina virtuale nella VNet e provare a raggiungere l'host di risorse: porta da qui. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* visualizzare un'applicazione in una macchina virtuale e testare l'accesso all'host e alla porta dalla console dall'app usando **tcpping**

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla propria rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, è possibile che la connessione VPN o ExpressRoute non sia configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* Le route non sono configurate con la subnet o gli intervalli di indirizzi del sito nel gateway locale.
* I gruppi di sicurezza di rete bloccano l'accesso per l'intervallo IP da punto a sito.
* I firewall locali bloccano il traffico dall'intervallo di indirizzi IP da punto a sito.
* Si sta tentando di raggiungere un indirizzo non RFC 1918 usando la funzionalità di integrazione VNet a livello di area.