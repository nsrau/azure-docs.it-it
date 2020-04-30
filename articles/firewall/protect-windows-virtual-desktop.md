---
title: Usare il firewall di Azure per proteggere il desktop virtuale Windows
description: Informazioni su come usare il firewall di Azure per proteggere le distribuzioni di desktop virtuali Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: victorh
ms.openlocfilehash: c5d7281d50c151722303b48b2b28a597eec72d79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254174"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Usare il firewall di Azure per proteggere le distribuzioni di desktop virtuali Windows

Windows Virtual Desktop (WVD) è un servizio di virtualizzazione di app e desktop eseguito in Azure. Quando un utente finale si connette a un ambiente desktop virtuale Windows, la sessione viene eseguita da un pool host. Un pool di host è una raccolta di macchine virtuali di Azure che si registrano in un desktop virtuale Windows come host di sessione. Queste macchine virtuali vengono eseguite nella rete virtuale e sono soggette ai controlli di sicurezza della rete virtuale. Per funzionare correttamente, è necessario l'accesso Internet in uscita al servizio WVD e potrebbe essere necessario l'accesso a Internet in uscita per gli utenti finali. Il firewall di Azure consente di bloccare l'ambiente e filtrare il traffico in uscita.

[![Architettura](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) del desktop virtuale Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Seguire le linee guida in questo articolo per fornire protezione aggiuntiva per il pool di host WVD usando il firewall di Azure.

## <a name="prerequisites"></a>Prerequisiti


 - Un ambiente WVD distribuito e un pool host.

   Per altre informazioni, vedere [esercitazione: creare un pool di host usando Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) e [creare un pool di host con un modello di Azure Resource Manager](../virtual-desktop/create-host-pools-arm-template.md).

Per ulteriori informazioni sugli ambienti WVD, vedere [ambiente desktop virtuale di Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Accesso in uscita al pool host per desktop virtuale Windows

Per il corretto funzionamento delle macchine virtuali di Azure create per desktop virtuale Windows deve essere possibile accedere a diversi nomi di dominio completi (FQDN). Il firewall di Azure fornisce un tag FQDN del desktop virtuale Windows per semplificare questa configurazione. Per consentire il traffico della piattaforma WVD in uscita, attenersi alla procedura seguente:

- Distribuire il firewall di Azure e configurare la route definita dall'utente (UDR) della subnet del pool host WVD per indirizzare tutto il traffico attraverso il firewall di Azure. La route predefinita ora punta al firewall.
- Creare una raccolta di regole dell'applicazione e aggiungere una regola per abilitare il tag FQDN *WindowsVirtualDesktop* . L'intervallo di indirizzi IP di origine è la rete virtuale del pool host, il protocollo è **https**e la destinazione è **WindowsVirtualDesktop**.

- Il set di account di archiviazione e di Service Bus necessari per il pool di host WVD è specifico della distribuzione, quindi non è ancora acquisito nel tag FQDN WindowsVirtualDesktop. È possibile risolvere il problema in uno dei modi seguenti:

   - Consentire l'accesso HTTPS dalla subnet del pool host a * xt.blob.core.windows.net, * eh.servicebus.windows.net e * xt.table.core.windows.net. Questi FQDN con caratteri jolly abilitano l'accesso richiesto, ma sono meno restrittivi.
   - Usare la query di log Analytics seguente per elencare gli FQDN esatti necessari e quindi consentirli in modo esplicito nelle regole dell'applicazione firewall:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Creare una raccolta di regole di rete aggiungere le regole seguenti:

   - Consenti DNS: consente il traffico dall'aggiunta di un indirizzo IP privato a * per le porte TCP e UDP 53.
   - Consenti KMS: consente il traffico dalle macchine virtuali WVD al servizio di attivazione Windows porta TCP 1688. Per ulteriori informazioni sugli indirizzi IP di destinazione, vedere [errore di attivazione di Windows nello scenario di tunneling forzato](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Alcune distribuzioni potrebbero non avere bisogno di regole DNS, ad esempio Azure Active Directory controller di dominio che inviano query DNS a DNS di Azure in 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Accesso in uscita al pool host a Internet

A seconda delle esigenze dell'organizzazione, può essere necessario abilitare l'accesso a Internet in uscita sicuro per gli utenti finali. Nei casi in cui l'elenco di destinazioni consentite sia ben definito, ad esempio [l'accesso a Office 365](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service), è possibile usare le regole di rete e l'applicazione del firewall di Azure per configurare l'accesso richiesto. In questo modo il traffico dell'utente finale viene indirizzato direttamente a Internet per ottenere prestazioni ottimali.

Se si vuole filtrare il traffico Internet degli utenti in uscita usando un gateway Web protetto locale esistente, è possibile configurare browser Web o altre applicazioni in esecuzione nel pool di host WVD con una configurazione esplicita del proxy. Vedere ad esempio [come usare le opzioni della riga di comando di Microsoft Edge per configurare le impostazioni del proxy](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Queste impostazioni proxy incidono solo sull'accesso a Internet dell'utente finale, consentendo al traffico in uscita della piattaforma WVD direttamente tramite il firewall di Azure.

## <a name="additional-considerations"></a>Altre considerazioni

Potrebbe essere necessario configurare regole del firewall aggiuntive, a seconda dei requisiti:

- Accesso al server NTP

   Per impostazione predefinita, le macchine virtuali che eseguono Windows si connettono a time.windows.com sulla porta UDP 123 per la sincronizzazione dell'ora. Creare una regola di rete per consentire l'accesso o per un server di ora in uso nell'ambiente.


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su desktop virtuale Windows: informazioni sul [desktop virtuale Windows](../virtual-desktop/overview.md)