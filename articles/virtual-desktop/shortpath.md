---
title: Desktop virtuale Windows RDP ShortPath (anteprima)
titleSuffix: Azure
description: Come configurare RDP ShortPath (anteprima) per desktop virtuale di Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 6ffe631dc237e7efaf1d6bfd9ac79ab7431c7371
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023140"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Desktop virtuale Windows RDP ShortPath (anteprima)

> [!IMPORTANT]
> Il shortpath RDP è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP ShortPath è una funzionalità di desktop virtuale di Windows che stabilisce un trasporto diretto basato su UDP tra Desktop remoto client e host sessione. Il protocollo RDP utilizza questo trasporto per fornire Desktop remoto e RemoteApp offrendo una maggiore affidabilità e una latenza coerente.

## <a name="key-benefits"></a>Vantaggi principali

* Il trasporto ShortPath RDP si basa su  [URCP (Universal Rate Control Protocol)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)estremamente efficiente. URCP ottimizza UDP con il monitoraggio attivo delle condizioni della rete e fornisce un utilizzo corretto e completo dei collegamenti. URCP funziona a livelli di ritardo e di perdita Bassi, in base alle esigenze Desktop remoto. URCP consente di ottenere prestazioni ottimali mediante l'apprendimento dinamico dei parametri di rete e il protocollo con un meccanismo di controllo della velocità.
* RDP ShortPath stabilisce la connettività diretta tra Desktop remoto client e l'host sessione. La connettività diretta riduce la dipendenza dai gateway desktop virtuali di Windows, migliora l'affidabilità della connessione e aumenta la larghezza di banda disponibile per ogni sessione utente.
* La rimozione del relè aggiuntivo riduce il tempo di round trip, che migliora l'esperienza utente con le applicazioni sensibili alla latenza e i metodi di input.
* RDP ShortPath offre supporto per la [configurazione della priorità di qualità del servizio (QoS) per le](./rdp-quality-of-service-qos.md) connessioni RDP tramite un servizi differenziati contrassegni di codice (DSCP)
* Il trasporto ShortPath RDP consente di [limitare il traffico di rete in uscita](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) specificando una velocità di limitazione per ogni sessione.

## <a name="connection-security"></a>Sicurezza delle connessioni

Il shortpath RDP sta estendendo le funzionalità di multitrasporto RDP. Non sostituisce il trasporto di connessione inversa ma lo completa. Tutto il broker di sessione iniziale viene gestito tramite l'infrastruttura desktop virtuale di Windows.

La porta UDP 3390 viene usata solo per il traffico ShortPath in ingresso autenticato sul trasporto di connessione inversa. Il listener ShortPath RDP ignora tutti i tentativi di connessione al listener a meno che non corrispondano alla sessione di connessione inversa.

Il shortpath RDP usa una connessione TLS tra il client e l'host sessione usando i certificati dell'host sessione. Per impostazione predefinita, il certificato usato per la crittografia RDP viene generato automaticamente dal sistema operativo durante la distribuzione. Se lo si desidera, i clienti possono distribuire certificati gestiti centralmente emessi dall'autorità di certificazione globale (Enterprise). Per ulteriori informazioni sulle configurazioni dei certificati, vedere la [documentazione di Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>Sequenza di connessione ShortPath RDP

Dopo l'installazione del [trasporto di connessione inversa](./network-connectivity.md), il client e l'host sessione stabiliscono la connessione RDP e negoziano le funzionalità di multitrasporto. Passaggi aggiuntivi descritti di seguito:

1. L'host sessione invia al client l'elenco dei relativi indirizzi IPv4 e IPv6 pubblici e privati.
2. Il client avvia il thread in background per stabilire un trasporto basato su UDP parallelo direttamente a uno degli indirizzi IP dell'host.
3. Mentre il client sta effettuando il probe degli indirizzi IP forniti, continua la creazione della connessione iniziale sul trasporto con connessione inversa per evitare un ritardo nella connessione utente.
4. Se il client ha una linea di visione diretta e la configurazione del firewall è corretta, il client stabilisce una connessione TLS sicura con l'host sessione.
5. Dopo aver stabilito il trasporto ShortPath, RDP sposta tutti i canali virtuali dinamici (DVCs), tra cui la grafica remota, l'input e il reindirizzamento del dispositivo al nuovo trasporto.
6. Se un firewall o una topologia di rete impedisce al client di stabilire una connettività UDP diretta, il protocollo RDP continua con un trasporto con connessione inversa.

Il diagramma seguente fornisce una panoramica di alto livello della connessione di rete ShortPath RDP.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagramma delle connessioni di rete ShortPath RDP" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Requisiti

Per supportare ShortPath RDP, il client desktop virtuale di Windows necessita di una linea di visibilità diretta per l'host sessione. È possibile ottenere una linea di visione diretta usando una delle tecnologie seguenti:

* [Peering privato di ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
* [VPN da sito a sito (basata su IPsec)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN da punto a sito (basata su IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Assegnazione di un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md)

Se si usano altri tipi di VPN per connettersi alla rete virtuale di Azure, è consigliabile usare la VPN basata su UDP per ottenere risultati ottimali. Sebbene la maggior parte delle soluzioni VPN basate su TCP incapsulano tutti i pacchetti IP, incluso UDP, aggiungono sovraccarico ereditato del controllo di congestione TCP che potrebbe rallentare le prestazioni RDP.

La linea di osservazione diretta indica che i firewall non bloccano la porta UDP 3390 e il client può connettersi direttamente all'host sessione.

## <a name="enabling-rdp-shortpath-preview"></a>Abilitazione di RDP ShortPath Preview

Per partecipare all'anteprima di ShortPath RDP, è necessario abilitare il listener ShortPath RDP nell'host sessione. È possibile abilitare il shortpath RDP su un numero qualsiasi di host di sessione utilizzati nell'ambiente in uso. Non è necessario abilitare il shortpath RDP in tutti gli host nel pool.
Per abilitare il listener ShortPath, è necessario configurare i seguenti valori del registro di sistema:

> [!WARNING]
> Potrebbero verificarsi gravi problemi se il registro di sistema viene modificato in modo errato mediante l'editor del registro di sistema o utilizzando un altro metodo. Questi problemi potrebbero richiedere la reinstallazione del sistema operativo. Microsoft non garantisce la possibilità di risolvere questi problemi. La modifica del Registro di sistema è a rischio e pericolo dell'utente.

1. Nell'host sessione avviare Regedit.exe, quindi passare al percorso seguente:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Creare un nuovo valore **DWORD** denominato **fUseUdpPortRedirector** e impostarlo su **1** (decimale)
3. Creare un nuovo valore **DWORD** denominato **UdpPortNumber** e impostarlo su **3390** (decimale)
4. Chiudere l'Editor del Registro di sistema.
5. Riavvia host sessione

È anche possibile eseguire i cmdlet seguenti in una finestra di PowerShell con privilegi elevati per impostare questi valori del registro di sistema:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

È anche possibile usare PowerShell per configurare criteri di gruppo

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Configurare Windows Defender Firewall con sicurezza avanzata

Per consentire il traffico di rete in ingresso per ShortPath RDP, usare il nodo Windows Defender Firewall con protezione avanzata nello snap-in MMC di gestione Criteri di gruppo per creare regole del firewall.

1. Aprire il Console Gestione Criteri di gruppo a [Windows Defender Firewall con sicurezza avanzata](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. Nel riquadro di spostamento selezionare **Regole connessioni in ingresso**.
3. Selezionare **azione**, quindi selezionare **nuova regola**.
4. Nella pagina **tipo di regola** della creazione guidata nuova regola connessioni in ingresso selezionare **personalizzata**, quindi fare clic su **Avanti**.
5. Nella pagina **programma** selezionare **percorso programma** e digitare "% SystemRoot% \system32\svchost.exe" e quindi fare clic su **Avanti**.
6. Nella pagina **protocollo e porte** selezionare il tipo di protocollo UDP. Nella **porta locale** selezionare "porte specifiche" e digitare 3390.
7. Nella pagina **Ambito** specificare che la regola si applica unicamente al traffico di rete in entrata o in uscita dagli indirizzi IP immessi in questa pagina. Configurare nel modo appropriato per la progettazione e quindi fare clic su **Avanti**.
8. Nella pagina **azione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.
9. Nella pagina **profilo** selezionare i tipi di percorsi di rete a cui si applica questa regola e quindi fare clic su **Avanti**.
10. Nella pagina **nome** Digitare un nome e una descrizione per la regola e quindi fare clic su **fine**.

È possibile verificare che la nuova regola corrisponda agli screenshot seguenti: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="screenshot della scheda generale per la configurazione del firewall per le connessioni di rete SHORTPATH RDP" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Screenshot della scheda programmi e servizi per la configurazione del firewall per le connessioni di rete ShortPath RDP" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Screenshot della scheda protocolli e porte per la configurazione del firewall per le connessioni di rete ShortPath RDP" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

È anche possibile usare PowerShell per configurare Windows Firewall:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Uso di PowerShell per la configurazione di Windows Defender Firewall

È anche possibile usare PowerShell per configurare criteri di gruppo

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Configurazione del gruppo di sicurezza di rete di Azure

Per consentire l'accesso al listener ShortPath RDP attraverso i limiti di sicurezza della rete, è necessario configurare il gruppo di sicurezza di rete di Azure per consentire la porta UDP in ingresso 3390.
Seguire la [documentazione del gruppo di sicurezza di rete](../virtual-machines/windows/nsg-quickstart-portal.md) per creare una regola di sicurezza in ingresso che consenta il traffico con i parametri seguenti:

* **Origine**  -  dati **Qualsiasi** o l'intervallo di indirizzi IP in cui risiedono i client
* **Intervalli di porte di origine** -* *\** _ _ **destinazione**  -  **any**
* Intervalli di porte di **destinazione**  -  **3390**
* **Protocollo**  -  di **UDP**
* **Azione**  -  **Consenti**
* Facoltativamente, modificare la **priorità**. La priorità determina l'ordine in cui vengono applicate le regole: più basso è il valore numerico, prima viene applicata la regola.
* **Nome** -- **ShortPath RDP**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Disabilitazione di ShortPath RDP per una subnet specifica

Se è necessario bloccare le subnet specifiche usando il trasporto ShortPath RDP, è possibile configurare gruppi di sicurezza di rete aggiuntivi che specificano gli intervalli di indirizzi IP di origine.

## <a name="verifying-the-connectivity"></a>Verifica della connettività

### <a name="using-connection-information-dialog"></a>Finestra di dialogo utilizzo delle informazioni di connessione

Per verificare che le connessioni stiano usando ShortPath RDP, aprire la finestra di dialogo "informazioni di connessione" facendo clic sull'icona dell'antenna nella barra degli strumenti della connessione.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Immagine della barra Connessione Desktop remoto":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Immagine della finestra di dialogo Connessione Desktop remoto info":::

### <a name="using-event-logs"></a>Uso dei registri eventi

Per verificare che la sessione usi il trasporto ShortPath RDP:

1. Connettersi al desktop della macchina virtuale usando il client Desktop virtuale Windows.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **registri applicazioni e servizi > microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Per determinare se viene usato il trasporto ShortPath RDP, cercare l'ID evento 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Uso di Log Analytics per verificare la connettività ShortPath

Se si usa [log Analytics di Azure](./diagnostics-log-analytics.md), è possibile monitorare le connessioni eseguendo una query sulla [tabella WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Una colonna denominata UdpUse indica se lo stack RDP del desktop virtuale di Windows utilizza il protocollo UDP per la connessione utente corrente.
I valori possibili sono:

* **0** : la connessione utente non usa il shortpath RDP
* **1** -la connessione utente usa ShortPath RDP
  
L'elenco di query seguente consente di esaminare le informazioni di connessione. È possibile eseguire questa query nell' [editor di query log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query). Per ogni query, sostituire `userupn` con l'UPN dell'utente che si desidera cercare.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="verify-shortpath-listener"></a>Verificare il listener ShortPath

Per verificare che il listener UDP sia abilitato, usare il comando di PowerShell seguente nell'host della sessione:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Se abilitata, verrà visualizzato un output simile al seguente:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Se si verifica un conflitto, è possibile identificare il processo che occupa la porta usando il comando seguente

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Disabilitazione di ShortPath RDP

In alcuni casi, potrebbe essere necessario disabilitare il trasporto ShortPath RDP. È possibile disabilitare RDP ShortPath usando criteri di gruppo.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Disabilitazione di ShortPath RDP nel client

Per disabilitare il shortpath RDP per un client specifico, è possibile usare la Criteri di gruppo seguente per disabilitare il supporto UDP:

1. Sul client eseguire **gpedit. msc**.
2. Passare a **Configurazione Computer > modelli di amministrazione > componenti di Windows > Servizi Desktop remoto > Client connessione Desktop remoto**.
3. Impostare l'impostazione **"Disattiva UDP sul client"** su **abilitato**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Disabilitazione di ShortPath RDP nell'host sessione

Per disabilitare il shortpath RDP per un host sessione specifico, è possibile usare la Criteri di gruppo seguente per disabilitare il supporto UDP:

1. Nell'host sessione eseguire **gpedit. msc**.
2. Passare a **Configurazione Computer > modelli di amministrazione > componenti di Windows > Servizi Desktop remoto > connessione Desktop remoto host > connessioni**.
3. Impostare l'impostazione **"Seleziona protocolli di trasporto RDP"** su **solo TCP**

## <a name="public-preview-feedback"></a>Commenti sull'anteprima pubblica

Vorremmo ricevere informazioni sulle tue esperienze con questa anteprima pubblica.
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla connettività di rete per desktop virtuali Windows, vedere [informazioni sulla connettività di rete per desktop virtuali Windows](network-connectivity.md).
* Per iniziare a usare la qualità del servizio (QoS) per desktop virtuale di Windows, vedere [implementare la qualità del servizio (QoS) per desktop virtuale di Windows](rdp-quality-of-service-qos.md).