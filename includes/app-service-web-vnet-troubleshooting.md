---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: ff54d60573fbc7b6694b8d02d1378869674c1e81
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050288"
---
La funzionalità è facile da configurare, ma ciò non significa che la tua esperienza sarà priva di problemi. Se si verificano problemi durante l'accesso all'endpoint desiderato, sono disponibili alcune utilità che è possibile usare per testare la connettività dalla console app. Le console disponibili sono due: Uno è la console Kudu e l'altro è la console nella portale di Azure. Per accedere alla console Kudu dall'app, passare a **strumenti**  >  **Kudu**. È anche possibile accedere alla console Kudo in [SiteName]. SCM. azurewebsites. NET. Al termine del caricamento del sito Web, passare alla scheda **debug console** . Per accedere alla console ospitata da portale di Azure dall'app, passare a **strumenti**  >  **console**.

#### <a name="tools"></a>Strumenti
Gli strumenti **ping**, **nslookup**e **tracert** non funzioneranno tramite la console a causa di vincoli di sicurezza. Per riempire il void, vengono aggiunti due strumenti distinti. Per testare la funzionalità DNS, è stato aggiunto uno strumento denominato **nameresolver.exe**. La sintassi è:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Si può usare nameresolver per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se si dispone di una configurazione non configurata correttamente con il DNS o se non si ha accesso al server DNS. È possibile visualizzare il server DNS usato dall'app nella console esaminando le variabili di ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

È possibile utilizzare lo strumento successivo per verificare la connettività TCP a una combinazione di host e porta. Questo strumento viene chiamato **tcpping** la cui sintassi è:

```console
tcpping.exe hostname [optional: port]
```

L'utilità **tcpping** indica se è possibile raggiungere un host e una porta specifici. Può mostrare l'esito positivo solo se un'applicazione è in ascolto sulla combinazione di host e porta e se l'app e l'host specificati hanno accesso alla rete.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Eseguire il debug dell'accesso alle risorse ospitate nella rete virtuale
Una serie di elementi può impedire all'app di raggiungere un host e una porta specifici. Nella maggior parte dei casi, si tratta di una delle operazioni seguenti:

* **L'ostacolo è rappresentato da un firewall**. Se si dispone di un firewall nel modo in cui si raggiunge il timeout TCP. che in questo caso è di 21 secondi. Usare lo strumento **tcpping** per testare la connettività. I timeout TCP possono essere causati da molti elementi oltre i firewall, ma iniziare da qui.
* **Il DNS non è accessibile**. Il timeout DNS è di 3 secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale. Se non è accessibile, è possibile che si disponga di un firewall o di un NSG che blocca l'accesso al DNS o che potrebbe essere inattivo.

Se tali elementi non rispondono ai problemi, cercare prima di tutto, ad esempio:

**Integrazione rete virtuale a livello di area**
* La destinazione è un indirizzo non RFC1918 e non si ha WEBSITE_VNET_ROUTE_ALL impostato su 1?
* Esiste una NSG che blocca l'uscita dalla subnet di integrazione?
* Se si usa Azure ExpressRoute o una VPN, il gateway locale è configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, verificare le route.
* Si dispone di autorizzazioni sufficienti per impostare la delega sulla subnet di integrazione? Durante la configurazione dell'integrazione VNet a livello di area, la subnet di integrazione viene delegata a Microsoft. Web. L'interfaccia utente di integrazione di VNet delega automaticamente la subnet a Microsoft. Web. Se l'account non dispone di autorizzazioni di rete sufficienti per impostare la delega, sarà necessario un utente che può impostare gli attributi nella subnet di integrazione per delegare la subnet. Per delegare manualmente la subnet di integrazione, passare all'interfaccia utente della subnet della rete virtuale di Azure e impostare la delega per Microsoft. Web.

**Gateway: integrazione VNet necessaria**
* L'intervallo di indirizzi da punto a sito è compreso negli intervalli RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Il gateway viene visualizzato come attivo nel portale? Se il gateway è inattivo, è necessario riattivarlo.
* I certificati vengono visualizzati come sincronizzati o si ritiene che la configurazione di rete sia stata modificata?  Se i certificati non sono sincronizzati o si ritiene che sia stata apportata una modifica alla configurazione della rete virtuale che non è stata sincronizzata con gli ASP, selezionare **Sincronizza rete**.
* Se si attraversa una VPN, è il gateway locale configurato per instradare il traffico al backup in Azure? Se è possibile raggiungere gli endpoint nella rete virtuale ma non in locale, verificare le route.
* Si sta provando a usare un gateway di coesistenza che supporta sia da punto a sito che da ExpressRoute? I gateway di coesistenza non sono supportati con l'integrazione VNet.

Il debug dei problemi di rete è una sfida perché non è possibile vedere ciò che blocca l'accesso a una combinazione host: porta specifica. Di seguito sono riportate alcune cause:

* Nell'host è presente un firewall che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. L'attraversamento delle subnet richiede spesso l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* Il nome host o l'indirizzo IP non è corretto.
* L'applicazione è in ascolto su una porta diversa da quella prevista. È possibile associare l'ID di processo alla porta di ascolto mediante "netstat -aon" nell'host dell'endpoint.
* I gruppi di sicurezza di rete vengono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Non si conosce l'indirizzo effettivamente usato dall'app. Potrebbe trattarsi di qualsiasi indirizzo nell'intervallo di indirizzi da punto a sito o subnet di integrazione, quindi è necessario consentire l'accesso dall'intero intervallo di indirizzi.

Di seguito è riportata la procedura di debug aggiuntiva:

* Connettersi a una macchina virtuale nella rete virtuale e provare a raggiungere l'host di risorse: porta da qui. Per verificare l'accesso al protocollo TCP usare il comando di PowerShell **test-netconnection**. La sintassi è:

```powershell
test-netconnection hostname [optional: -Port]
```

* Visualizzare un'applicazione in una macchina virtuale e testare l'accesso all'host e alla porta dalla console dall'app usando **tcpping**.

#### <a name="on-premises-resources"></a>Risorse locali ####

Se l'app non riesce a raggiungere una risorsa locale, verificare se è possibile raggiungere la risorsa dalla rete virtuale. Usare il comando di PowerShell **test-netconnection** per eseguire controllare l'accesso al protocollo TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, è possibile che la connessione VPN o ExpressRoute non sia configurata correttamente.

Se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non può, la causa è probabilmente uno dei motivi seguenti:

* Le route non sono configurate con la subnet o gli intervalli di indirizzi da punto a sito nel gateway locale.
* I gruppi di sicurezza di rete bloccano l'accesso per l'intervallo IP da punto a sito.
* I firewall locali bloccano il traffico dall'intervallo di indirizzi IP da punto a sito.
* Si sta tentando di raggiungere un indirizzo non RFC 1918 usando la funzionalità di integrazione VNet a livello di area.