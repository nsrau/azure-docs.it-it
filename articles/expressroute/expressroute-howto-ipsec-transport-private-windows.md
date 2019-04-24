---
title: 'Configurare la modalità di trasporto IPsec per host Windows - peering privato: ExpressRoute: Azure | Microsoft Docs'
description: Come abilitare la modalità trasporto IPSec tra macchine virtuali Windows di Azure e host Windows locali tramite il peering privato ExpressRoute usando oggetti Criteri di gruppo e unità organizzative.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367272"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configurare la modalità trasporto IPSec per il peering privato ExpressRoute

Questo articolo consente di creare tunnel IPSec in modalità trasporto tramite peering privato ExpressRoute tra macchine virtuali di Azure che eseguono Windows e host Windows locali. Le procedure in questo articolo permettono di creare questa configurazione usando oggetti Criteri di gruppo. Sebbene sia possibile creare questa configurazione senza usare unità organizzative e oggetti Criteri di gruppo, la combinazione di unità organizzative e oggetti Criteri di gruppo consente di semplificare il controllo dei criteri di sicurezza e permette una rapida scalabilità verticale. Le procedure in questo articolo presuppongono che sia già disponibile una configurazione di Active Directory e che si abbia familiarità con l'uso di unità organizzative e oggetti Criteri di gruppo.

## <a name="about-this-configuration"></a>Informazioni sulla configurazione

La configurazione nelle procedure seguenti usa una singola rete virtuale di Azure con il peering privato ExpressRoute. Questa configurazione può tuttavia essere estesa a più reti virtuali di Azure e reti locali. Questo articolo aiuta a definire criteri di crittografia IPSec e ad applicarli a un gruppo di macchine virtuali di Azure e host locali che fanno parte della stessa unità organizzativa. Viene configurata la crittografia tra macchine virtuali di Azure (vm1 e vm2) e l'host locale host1 solo per il traffico HTTP destinato alla porta 8080. In base ai requisiti, è possibile creare tipi diversi di criteri IPSec.

### <a name="working-with-ous"></a>Uso di unità organizzative 

I criteri di sicurezza associati a un'unità organizzativa vengono inviati ai computer tramite un oggetto Criteri di gruppo. Ecco alcuni vantaggi che derivano dall'usare unità organizzative al posto di applicare criteri a un singolo host:

* L'associazione di criteri a un'unità organizzativa garantisce che i computer che appartengono alla stessa unità organizzativa ottengano gli stessi criteri.
* Se si modificano i criteri di sicurezza associati all'unità organizzativa, le modifiche vengono applicate a tutti gli host nell'unità organizzativa.

### <a name="diagrams"></a>Diagrammi

Il diagramma seguente illustra l'interconnessione e lo spazio indirizzi IP assegnato. Le macchine virtuali di Azure e l'host locale sono in esecuzione in Windows 2016. Le macchine virtuali di Azure e l'host locale host1 fanno parte dello stesso dominio. Le macchine virtuali di Azure e gli host locali possono risolvere correttamente i nomi tramite DNS.

[![1]][1]

Questo diagramma illustra i tunnel IPSec in transito nel peering privato ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Uso di criteri IPSec

In Windows la crittografia è associata ai criteri IPSec. I criteri IPSec determinano il tipo di traffico IP che viene protetto e il meccanismo di sicurezza applicato ai pacchetti IP.
I **criteri IPSec** sono composti dagli elementi seguenti: **elenchi di filtri**, **operazioni di filtro** e **regole di sicurezza**.

Quando si configurano i criteri IPSec, è importante comprendere la terminologia seguente:

* **Criteri IPsec:** una raccolta di regole. In un determinato momento può essere attivo ("assegnato") un solo criterio. Ogni criterio può avere una o più regole, che possono essere attive contemporaneamente. A un computer può venire assegnato solo un criterio IPSec attivo in un determinato momento. Tuttavia, all'interno di un criterio IPSec, è possibile definire più operazioni che possono essere eseguite in situazioni diverse. Ogni set di regole IPSec è associato a un elenco di filtri che influiscono sul tipo di traffico di rete a cui si applica la regola.

* **Elenchi di filtri:** gli elenchi di filtri sono aggregazioni di uno o più filtri. Un elenco può contenere più filtri. Un filtro definisce se la comunicazione è consentita, protetta o bloccata, in base a intervalli di indirizzi IP, protocolli o anche porte specifiche dei protocolli. Ogni filtro corrisponde a un particolare set di condizioni, ad esempio i pacchetti inviati da una subnet specifica a un determinato computer su una porta di destinazione specifica. Quando le condizioni di rete corrispondono a uno o più filtri, l'elenco di filtri viene attivato. Ogni filtro è definito all'interno di un elenco di filtri specifico. I filtri non possono essere condivisi tra elenchi di filtri. Un determinato elenco di filtri può tuttavia essere incorporato in diversi criteri IPSec. 

* **Operazioni di filtro:** un metodo di sicurezza definisce un set di algoritmi di sicurezza, protocolli e chiavi offerti da un computer durante le negoziazioni IKE. Le operazioni di filtro sono elenchi di metodi di sicurezza, classificati in ordine di preferenza.  Quando un computer negozia una sessione IPSec, accetta o invia proposte in base all'impostazione di sicurezza archiviata nell'elenco di operazioni di filtro.

* **Regole di sicurezza:** le regole controllano come e quando i criteri IPSec proteggono la comunicazione. Vengono usati l'**elenco di filtri** e le **operazioni di filtro** per generare una regola IPSec per creare la connessione IPSec. Ogni criterio può avere una o più regole, che possono essere attive contemporaneamente. Ogni regola contiene un elenco di filtri IP e una raccolta di operazioni di sicurezza che vengono eseguite quando si verifica una corrispondenza con tale elenco di filtri:
  * Operazioni di filtro IP
  * Metodi di autenticazione
  * Impostazioni tunnel IP
  * Tipi di connessione

[![5]][5]

## <a name="before-you-begin"></a>Prima di iniziare

Accertarsi che siano soddisfatti i prerequisiti seguenti:

* È necessario disporre di una configurazione funzionante di Active Directory da usare per implementare le impostazioni di Criteri di gruppo. Per altre informazioni sugli oggetti Criteri di gruppo, vedere [Group Policy Objects](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx) (Oggetti Criteri di gruppo).

* È necessario avere un circuito ExpressRoute attivo.
  * Per informazioni sulla creazione di un circuito ExpressRoute, vedere [Creare un circuito ExpressRoute ](expressroute-howto-circuit-arm.md). 
  * Verificare che il circuito sia abilitato dal provider di connettività. 
  * Verificare che per il circuito sia configurato il peering privato Azure. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-arm.md) per istruzioni relative al routing. 
  * Verificare di disporre di una rete virtuale e di un gateway di rete virtuale e che ne sia stato effettuato il provisioning completo. Seguire le istruzioni per [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Un gateway di rete virtuale per ExpressRoute usa 'ExpressRoute' come GatewayType, non VPN.

* Il gateway di rete virtuale ExpressRoute deve essere connesso al circuito ExpressRoute. Per altre informazioni, vedere [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Verificare che le macchine virtuali Windows di Azure siano distribuite nella rete virtuale.

* Verificare che ci sia connettività tra gli host locali e le macchine virtuali di Azure.

* Verificare che le macchine virtuali Windows di Azure e gli host locali siano in grado di risolvere correttamente i nomi tramite DNS.

### <a name="workflow"></a>Flusso di lavoro

1. Creare un oggetto Criteri di gruppo e associarlo all'unità organizzativa.
2. Definire un'**operazione di filtro** IPSec.
3. Definire un **elenco di filtri** IPSec.
4. Creare un criterio IPSec con **regole di sicurezza**.
5. Assegnare l'oggetto Criteri di gruppo IPSec all'unità organizzativa.

### <a name="example-values"></a>Valori di esempio

* **Nome di dominio:** ipsectest.com

* **Unità organizzativa:** IPSecOU

* **Computer Windows locale:** host1

* **Macchine virtuali Windows di Azure:** vm1, vm2

## <a name="creategpo"></a>1. Creare un oggetto Criteri di gruppo

1. Per creare un nuovo oggetto Criteri di gruppo collegato a un'unità organizzativa, aprire lo snap-in Gestione Criteri di gruppo e individuare l'unità organizzativa a cui l'oggetto Criteri di gruppo verrà collegato. Nell'esempio il nome dell'unità organizzativa è **IPSecOU**. 

   [![9]][9]
2. Nello snap-in Gestione Criteri di gruppo selezionare l'unità organizzativa e fare clic con il pulsante destro del mouse. Dal menu a discesa scegliere "**Crea un oggetto Criteri di gruppo in questo dominio e crea qui un collegamento**".

   [![10]][10]
3. Assegnare all'oggetto Criteri di gruppo un nome intuitivo in modo che sia possibile individuarlo facilmente in seguito. Fare clic su **OK** per creare l'oggetto Criteri di gruppo e il collegamento.

   [![11]][11]

## <a name="enablelink"></a>2. Abilitare il collegamento dell'oggetto Criteri di gruppo

Per applicare l'oggetto Criteri di gruppo all'unità organizzativa, non è sufficiente che l'oggetto Criteri di gruppo sia collegato all'unità organizzativa, ma è anche necessario abilitare il collegamento.

1. Individuare l'oggetto Criteri di gruppo creato, fare clic con pulsante destro del mouse e scegliere **Modifica** dal menu a discesa.
2. Per applicare l'oggetto Criteri di gruppo all'unità organizzativa, selezionare **Collegamento abilitato**.

   [![12]][12]

## <a name="filteraction"></a>3. Definire l'operazione di filtro IP

1. Nell'elenco a discesa fare clic con il pulsante destro del mouse su **Criteri di sicurezza IP su Active Directory** e quindi scegliere **Gestisci elenchi filtri IP e operazioni filtro**.

   [![15]][15]
2. Nella scheda "**Gestione operazioni filtro**" fare clic su **Aggiungi**.

   [![16]][16]

3. Nella pagina **Impostazione guidata operazione filtro sicurezza IP** fare clic su **Avanti**.

   [![17]][17]
4. Assegnare all'operazione di filtro un nome intuitivo in modo che sia possibile trovarla in seguito. In questo esempio il nome dell'operazione di filtro è **myEncryption**. È anche possibile aggiungere una descrizione. Quindi fare clic su **Next**.

   [![18]][18]
5. L'opzione **Negozia sicurezza** consente di definire il comportamento se non è possibile abilitare IPSec per la comunicazione con un altro computer. Selezionare **Negozia sicurezza** e quindi fare clic su **Avanti**.

   [![19]][19]
6. Nella pagina **Comunicazione con computer che non supportano IPSec** selezionare **Non consentire comunicazioni non sicure** e quindi fare clic su **Avanti**.

   [![20]][20]
7. Nella pagina **Sicurezza traffico IP** selezionare **Personalizzata** e quindi fare clic su **Impostazioni**.

   [![21]][21]
8. Nella pagina **Impostazioni personalizzate metodo di sicurezza** selezionare **Integrità dati con crittografia (ESP): SHA1, 3DES**. Fare quindi clic su **OK**.

   [![22]][22]
9. Nella pagina **Gestione operazioni filtro** è possibile vedere che il filtro **myEncryption** è stato aggiunto correttamente. Fare clic su **Close**.

   [![23]][23]

## <a name="filterlist1"></a>4. Definire un elenco di filtri IP

Creare un elenco di filtri che specifica il traffico HTTP crittografato destinato alla porta 8080.

1. Per definire il tipo di traffico che deve essere crittografato, usare un **elenco di filtri IP**. Nella scheda **Gestione elenchi filtri IP** fare clic su **Aggiungi** per aggiungere un nuovo elenco di filtri IP.

   [![24]][24]
2. Nel campo **Nome** digitare un nome per l'elenco di filtri IP. Ad esempio, **azure-onpremises-HTTP8080**. Fare quindi clic su **Aggiungi**.

   [![25]][25]
3. Nella pagina **Descrizione filtro IP e proprietà Speculare** selezionare **Speculare**. L'impostazione speculare consente di definire una corrispondenza con i pacchetti che viaggiano in entrambe le direzioni, permettendo la comunicazione bidirezionale. Quindi fare clic su **Next**.

   [![26]][26]
4. Nella pagina **Origine traffico IP**, nell'elenco a discesa **Indirizzo origine** scegliere **Subnet o indirizzo IP specifico**. 

   [![27]][27]
5. Specificare l'indirizzo di origine in **Subnet o indirizzo IP** per il traffico IP e quindi fare clic su **Avanti**.

   [![28]][28]
6. Specificare il **Tipo di indirizzo di destinazione:** indirizzo IP o subnet. Quindi fare clic su **Next**.

   [![29]][29]
7. Nella pagina **Tipo protocollo IP** selezionare **TCP**. Quindi fare clic su **Next**.

   [![30]][30]
8. Nella pagina **Porta protocollo IP** selezionare **Da qualsiasi porta** e **A questa porta**. Digitare **8080** nella casella di testo. Queste impostazioni specificano che solo il traffico HTTP destinato alla porta 8080 verrà crittografato. Quindi fare clic su **Next**.

   [![31]][31]
9. Visualizzare l'elenco di filtri IP.  La configurazione dell'elenco di filtri IP **azure-onpremises-HTTP8080** attiva la crittografia per tutto il traffico che soddisfa i criteri seguenti:

   * Qualsiasi indirizzo di origine in 10.0.1.0/24 (Subnet2 Azure)
   * Qualsiasi indirizzo di destinazione in 10.2.27.0/25 (subnet locale)
   * Protocollo TCP
   * Porta di destinazione 8080

   [![32]][32]

## <a name="filterlist2"></a>5. Modificare l'elenco di filtri IP

Per crittografare lo stesso tipo di traffico nella direzione opposta (dall'host locale alla macchina virtuale di Azure) è necessario un secondo filtro IP. Il processo di configurazione del nuovo filtro corrisponde a quello usato per configurare il primo filtro IP. Le uniche differenze sono la subnet di origine e la subnet di destinazione.

1. Per aggiungere un nuovo filtro IP all'elenco di filtri IP, selezionare **Modifica**.

   [![33]][33]
2. Nella pagina **Elenco filtri IP** fare clic su **Aggiungi**.

   [![34]][34]
3. Creare un secondo filtro IP usando le impostazioni nell'esempio seguente:

   [![35]][35]
4. Dopo aver creato il secondo filtro IP, l'elenco di filtri IP sarà simile al seguente:

   [![36]][36]

Se è necessaria la crittografia tra una posizione locale e una subnet di Azure per proteggere un'applicazione, invece di modificare l'elenco di filtri IP esistente è possibile aggiungere un nuovo elenco di filtri IP. L'associazione di due elenchi di filtri IP allo stesso criterio IPSec offre una maggiore flessibilità, perché un elenco di filtri IP specifico può essere modificato o rimosso in qualsiasi momento senza alcun impatto sugli altri elenchi di filtri IP.

## <a name="ipsecpolicy"></a>6. Creare un criterio di sicurezza IPSec 

Creare un criterio IPSec con regole di sicurezza.

1. Selezionare la voce **Criteri di sicurezza IP su Active Directory** associata all'unità organizzativa. Fare clic con il pulsante destro del mouse e scegliere **Crea criterio di sicurezza IP**.

   [![37]][37]
2. Assegnare un nome al criterio di sicurezza. Ad esempio, **policy-azure-onpremises**. Quindi fare clic su **Next**.

   [![38]][38]
3. Fare clic su **Avanti** senza selezionare la casella di controllo.

   [![39]][39]
4. Verificare che la casella di controllo **Modifica proprietà** sia selezionata e quindi fare clic su **Fine**.

   [![40]][40]

## <a name="editipsec"></a>7. Modificare i criteri di sicurezza IPSec

Aggiungere i criteri IPSec all'**elenco di filtri IP** e all'**operazione di filtro** configurati in precedenza.

1. Nella scheda **Regole** della finestra delle proprietà dei criteri HTTP fare clic su **Aggiungi**.

   [![41]][41]
2. Nella pagina di benvenuto fare clic su **Avanti**.

   [![42]][42]
3. Una regola consente di definire la modalità IPSec: modalità tunnel o modalità trasporto.

   * In modalità tunnel il pacchetto originale viene incapsulato da un set di intestazioni IP. La modalità tunnel protegge le informazioni di routing interne crittografando l'intestazione IP del pacchetto originale. La modalità tunnel viene spesso implementata tra i gateway in scenari VPN da sito a sito. La modalità tunnel nella maggior parte dei casi viene usata per la crittografia end-to-end tra host.

   * La modalità trasporto crittografa solo il payload e il trailer ESP, mentre l'intestazione IP del pacchetto originale non viene crittografata. Nella modalità trasporto, l'origine IP e la destinazione IP dei pacchetti restano invariate.

   Selezionare **Questa regola non specifica un tunnel** e quindi fare clic su **Avanti**.

   [![43]][43]
4. **Tipo di rete** definisce la connessione di rete associata ai criteri di sicurezza. Selezionare **Tutte le connessioni di rete** e quindi fare clic su **Avanti**.

   [![44]][44]
5. Selezionare l'elenco di filtri IP creato in precedenza, **azure-onpremises-HTTP8080** e quindi fare clic su **Avanti**.

   [![45]][45]
6. Selezionare l'operazione di filtro esistente **myEncryption** creata in precedenza.

   [![46]][46]
7. Windows supporta quattro diversi tipi di autenticazione: Kerberos, con certificati, NTLMv2 e con chiave precondivisa. Poiché si stanno usando host aggiunti al dominio, selezionare **Impostazione predefinita di Active Directory (protocollo V5 Kerberos)** e quindi fare clic su **Avanti**.

   [![47]][47]
8. Il nuovo criterio crea la regola di sicurezza: **azure-onpremises-HTTP8080**. Fare clic su **OK**.

   [![48]][48]

Il criterio IPSec richiede che tutte le connessioni HTTP sulla porta di destinazione 8080 usino la modalità trasporto IPSec. Poiché HTTP è un protocollo di testo non crittografato, l'abilitazione del criterio di sicurezza garantisce che i dati vengano crittografati quando vengono trasferiti tramite il peering privato ExpressRoute. I criteri di sicurezza IP per Active Directory sono più complessi da configurare rispetto a Windows Firewall con sicurezza avanzata, ma consentono una maggiore personalizzazione della connessione IPSec.

## <a name="assigngpo"></a>8. Assegnare l'oggetto Criteri di gruppo IPSec all'unità organizzativa

1. Visualizzare il criterio. Il criterio di gruppo di sicurezza è definito ma non ancora assegnato.

   [![49]][49]
2. Per assegnare il criterio di gruppo di sicurezza all'unità organizzativa **IPSecOU**, fare clic con il pulsante destro del mouse sul criterio di sicurezza e scegliere **Assegna**.
   Il criterio di gruppo di sicurezza verrà assegnato a ogni computer appartenente all'unità organizzativa.

   [![50]][50]

## <a name="checktraffic"></a>Controllare la crittografia del traffico

Per testare la crittografia applicata dall'oggetto Criteri di gruppo all'unità organizzativa, installare IIS in tutte le macchine virtuali di Azure e in host1. Ogni istanza di IIS è personalizzata per rispondere alle richieste HTTP sulla porta 8080.
Per verificare la crittografia, è possibile installare uno sniffer di rete (ad esempio Wireshark) in tutti i computer nell'unità organizzativa.
Uno script di PowerShell funziona come client HTTP per generare le richieste HTTP sulla porta 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
L'acquisizione di rete seguente mostra i risultati per l'host locale host1 con il filtro ESP in modo da attivare la corrispondenza solo con il traffico crittografato:

[![51]][51]

Se si esegue lo script di PowerShell in locale (client HTTP), l'acquisizione di rete nella macchina virtuale di Azure mostra una traccia simile.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Diagramma di rete della modalità trasporto IPSec tramite ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "Traffico IPSec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Criteri IPSec Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Unità organizzativa in Criteri di gruppo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "Creazione di un oggetto Criteri di gruppo associato all'unità organizzativa"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "Assegnazione di un nome all'oggetto Criteri di gruppo associato all'unità organizzativa"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Modifica dell'oggetto Criteri di gruppo"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Gestione di elenchi di filtri IP e operazioni di filtro"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Aggiunta di un'operazione di filtro"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Impostazione guidata operazione"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Nome operazione di filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Operazione di filtro"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Definizione del comportamento se viene stabilita una connessione non sicura"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "Meccanismo di sicurezza"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Metodo di sicurezza personalizzato"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "Elenco operazioni di filtro"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Aggiunta di un nuovo elenco di filtri IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Aggiunta del traffico HTTP al filtro IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "Corrispondenza con pacchetti in entrambe le direzioni"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "Selezione della subnet di origine"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Rete di origine"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Rete di destinazione"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocollo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "Porta di origine e porta di destinazione"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Elenco di filtri"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "Elenco di filtri IP con traffico HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Aggiunta di un secondo filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "Elenco di filtri IP - seconda voce"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "Elenco di filtri IP - seconda voce"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Creazione criterio di sicurezza IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "Nome del criterio IPSec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Creazione guidata criterio IPSec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "Modifica del criterio IPSec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Aggiunta di una nuova regola di sicurezza al criterio IPSec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Creazione di una nuova regola di sicurezza"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Modalità trasporto"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Tipo di rete"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "Selezione dell'elenco di filtri IP esistente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "Selezione dell'operazione di filtro esistente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Selezione del metodo di autenticazione"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "Fine del processo di creazione del criterio di sicurezza"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Criterio IPSec collegato all'oggetto Criteri di gruppo ma non assegnato"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Criterio IPSec assegnato all'oggetto Criteri di gruppo"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Acquisizione del traffico IPSec crittografato"
