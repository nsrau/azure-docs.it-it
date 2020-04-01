---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419577"
---
La funzione è facile da configurare, ma questo non significa che la tua esperienza sarà senza problemi. Se si verificano problemi di accesso all'endpoint desiderato, esistono alcune utilità che è possibile usare per verificare la connettività dalla console dell'app. Le console disponibili sono due: Uno è la console Kudu e l'altro è la console nel portale di Azure.One is the Kudu console, and the other is the console in the Azure portal. Per raggiungere la console Kudu dalla tua app, vai a **Tools** > **Kudu**. È anche possibile raggiungere la console di Kudo all'indirizzo [sitename].scm.azurewebsites.net.You can also reach the Kudo console at [sitename].scm.azurewebsites.net. Dopo il caricamento del sito Web, passare alla scheda **della console di debug.** Per accedere alla console ospitata dal portale di Azure dall'app, passare a**Console** **di strumenti** > .

#### <a name="tools"></a>Strumenti
Il **ping**degli strumenti , **nslookup**e **tracert** non funzioneranno attraverso la console a causa dei vincoli di sicurezza. Per riempire il vuoto, vengono aggiunti due strumenti separati. Per testare la funzionalità DNS, è stato aggiunto uno strumento denominato **nameresolver.exe**. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

Si può usare nameresolver per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se si dispone di qualcosa di non configurato correttamente con il DNS o forse non si ha accesso al server DNS. È possibile visualizzare il server DNS utilizzato dall'app nella console esaminando le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

È possibile utilizzare lo strumento successivo per verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità **tcpping** indica se è possibile raggiungere un host e una porta specifici. Può mostrare esito positivo solo se è presente un'applicazione in ascolto presso la combinazione di host e porta e l'accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Eseguire il debug dell'accesso alle risorse ospitate dalla rete virtualeDebug access to virtual network-hosted resources
Diversi elementi possono impedire all'app di raggiungere un host e una porta specifici. La maggior parte delle volte è una di queste cose:

* **L'ostacolo è rappresentato da un firewall**. Se si dispone di un firewall nel modo, si preme il timeout TCP. che in questo caso è di 21 secondi. Utilizzare lo strumento **tcpping** per verificare la connettività. I timeout TCP possono essere causati da molte cose oltre i firewall, ma iniziano da lì.
* **Il DNS non è accessibile**. Il timeout DNS è di 3 secondi per server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Non è possibile usare nslookup, perché non utilizza il DNS con cui è configurata la rete virtuale. Se non è accessibile, si potrebbe avere un firewall o NSG bloccando l'accesso al DNS o potrebbe essere inattivo.

Se questi elementi non rispondono ai tuoi problemi, cerca prima cose come:

**Integrazione della rete virtuale regionale**
* La destinazione è un indirizzo non RFC1918 e non è WEBSITE_VNET_ROUTE_ALL impostata su 1?
* Esiste un gruppo di sicurezza di rete che blocca l'uscita dalla subnet di integrazione?
* Se si passa attraverso Azure ExpressRoute o una VPN, il gateway locale è configurato per instradare il backup del traffico ad Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, controllare le route.
* Si dispone di autorizzazioni sufficienti per impostare la delega nella subnet di integrazione? Durante la configurazione dell'integrazione della rete virtuale regionale, la subnet di integrazione viene delegata a Microsoft.Web.During regional VNet Integration configuration, your integration subnet is delegated to Microsoft.Web. L'interfaccia utente di integrazione della rete virtuale delega automaticamente la subnet a Microsoft.Web.The VNet Integration UI delegates the subnet to Microsoft.Web automatically. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare la delega, è necessario un utente che possa impostare gli attributi nella subnet di integrazione per delegare la subnet. To manually delegate the integration subnet, go to the Azure Virtual Network subnet UI and set the delegation for Microsoft.Web.

**Integrazione della rete virtuale richiesta dal gateway**
* L'intervallo di indirizzi da punto a sito è compreso negli intervalli RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come in alto nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si sospetta che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si sospetta che sia stata apportata una modifica alla configurazione della rete virtuale che non è stata sincronizzata con gli ASP, selezionare **Sincronizza rete**.
* Se si passa attraverso una VPN, il gateway locale è configurato per instradare il traffico ad Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, controllare le route.
* Si sta tentando di usare un gateway di coesistenza che supporta sia il punto di punto che ExpressRoute? I gateway di coesistenza non sono supportati con l'integrazione della rete virtuale.

Il debug dei problemi di rete è una sfida perché non è possibile vedere cosa blocca l'accesso a una combinazione host:porta specifica. Alcune cause includono:

* Si dispone di un firewall all'interno dell'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. L'attraversamento di subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattivo.
* Avevi l'IP o il nome host sbagliato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint.
* I gruppi di sicurezza di rete sono configurati in modo tale da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Non sai quale indirizzo usa effettivamente la tua app. Potrebbe essere qualsiasi indirizzo nella subnet di integrazione o nell'intervallo di indirizzi da punto a sito, pertanto è necessario consentire l'accesso dall'intero intervallo di indirizzi.

Di seguito è riportata la procedura di debug aggiuntiva:

* Connettersi a una macchina virtuale nella rete virtuale e tentare di raggiungere l'host delle risorse: porta da lì. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

      test-netconnection hostname [optional: -Port]

* Visualizzare un'applicazione in una macchina virtuale e verificare l'accesso a tale host e porta dalla console dall'app usando **tcpping**.

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verifica di poter raggiungere la risorsa dalla rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, la connessione VPN o ExpressRoute potrebbe non essere configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale, ma l'app non lo è, la causa è probabilmente uno dei motivi seguenti:If your virtual network-hosted VM can reach your on-premises system but your app't can no, the cause is likely one of the following reasons:

* Le route non sono configurate con la subnet o gli intervalli di indirizzi da punto a sito nel gateway locale.
* I gruppi di sicurezza di rete bloccano l'accesso per l'intervallo IP da punto a sito.
* I firewall locali bloccano il traffico dall'intervallo IP da punto a sito.
* Si sta tentando di raggiungere un indirizzo non RFC 1918 utilizzando la funzionalità di integrazione della rete virtuale regionale.