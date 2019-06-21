---
title: Risolvere i problemi di attivazione della macchina virtuale Windows in Azure | Microsoft Docs
description: Fornisce la procedura per risolvere i problemi di attivazione della macchina virtuale Windows in Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: bc058cb3f27545b9e4ad8ef1062ca4d2fa4c9fa8
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155143"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Risolvere i problemi di attivazione della macchina virtuale Windows di Azure

Se si verificano problemi durante l'attivazione della macchina virtuale (VM) Windows di Azure creata da un'immagine personalizzata, è possibile usare le informazioni contenute in questo documento per risolvere il problema. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Informazioni sugli endpoint del servizio di gestione delle chiavi di Azure per l'attivazione del prodotto Windows di macchine virtuali di Azure

Azure usa endpoint diversi per l'attivazione del servizio di gestione delle chiavi, a seconda dell'area del cloud in cui risiede la macchina virtuale. Quando si usa questa guida alla risoluzione dei problemi, usare l'endpoint del servizio di gestione delle chiavi appropriato applicabile alla propria area.

* Aree del cloud pubblico di Azure: kms.core.windows.net:1688
* Aree del cloud nazionale cinese 21Vianet di Azure: kms.core.chinacloudapi.cn:1688
* Aree del cloud nazionale tedesco di Azure: kms.core.cloudapi.de:1688
* Aree del cloud nazionale US Gov di Azure: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Sintomo

Quando si cerca di attivare una VM Windows di Azure, si riceve un messaggio di errore simile all'esempio seguente:

**Errore: 0xC004F074 Servizio gestione licenze software: impossibile attivare il computer. Impossibile contattare un servizio di gestione delle chiavi. Per altre informazioni, vedere il registro eventi applicazioni.**

## <a name="cause"></a>Causa

In genere si verificano problemi di attivazione della macchina virtuale di Azure se la macchina virtuale Windows non viene configurata usando la chiave di configurazione del client del server di gestione delle chiavi appropriata, oppure se la macchina virtuale Windows ha un problema di connettività al server di gestione delle chiavi di Azure (kms.core.windows.net, porta 1688). 

## <a name="solution"></a>Soluzione

>[!NOTE]
>Se si usano una VPN da sito a sito e il tunneling forzato, vedere [Use Azure custom routes to enable KMS activation with forced tunneling](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) (Usare le route personalizzate di Azure per abilitare l'attivazione del Server di gestione delle chiavi con il tunneling forzato). 
>
>Se si usa ExpressRoute ed è stata pubblicata una route predefinita, vedere [Azure VM may fail to activate over ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx) (Potrebbe non essere possibile attivare la VM di Azure tramite ExpressRoute).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Passaggio 1 configurare la chiave di configurazione client KMS appropriata

Per la macchina virtuale creata da un'immagine personalizzata, è necessario configurare la chiave di configurazione client KMS appropriata per la macchina virtuale.

1. Eseguire **slmgr.vbs /dlv** in un prompt dei comandi con privilegi elevati. Controllare il valore Description nell'output e quindi determinare se è stato creato da un supporto per licenze al dettaglio (canale RETAIL) o per contratti multilicenza (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** indica il canale RETAIL, usare i comandi seguenti per impostare la [chiave di configurazione del client del Servizio di gestione delle chiavi](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) per la versione di Windows Server in uso e forzare un nuovo tentativo di attivazione: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Per Windows Server 2016 Datacenter, ad esempio, si userà il comando seguente:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Passaggio 2 Verificare la connettività tra la VM e il Servizio di gestione delle chiavi di Azure

1. Scaricare ed estrarre lo strumento [Psping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) in una cartella locale nella VM che non viene attivata. 

2. Andare a Start, cercare Windows PowerShell, fare clic con il pulsante destro del mouse su Windows PowerShell e scegliere Esegui come amministratore.

3. Verificare che la VM sia configurata per usare il server di gestione delle chiavi di Azure corretto. A tale scopo, usare il comando seguente:
  

    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Il comando dovrebbe restituire: nome del computer del Servizio di gestione delle chiavi impostato su kms.core.windows.net:1688.

4. Usando Psping verificare di avere la connettività al server di gestione delle chiavi. Passare alla cartella in cui si è estratto il download Pstools.zip e quindi eseguire quanto segue:
  

    ```
    \psping.exe kms.core.windows.net:1688
    ```

  
   Nelle righe dalla seconda all'ultima dell'output verificare che sia visualizzato: Sent = 4, Received = 4, Lost = 0 (0% loss).

   Se Lost è maggiore di 0 (zero), la VM non ha la connettività al server di gestione delle chiavi. In questo caso, se la VM è in una rete virtuale ed è specificato un server DNS personalizzato, è necessario verificare che il server DNS possa risolvere kms.core.windows.net. In alternativa, sostituire il server DNS con uno che risolve kms.core.windows.net.

   Tenere presente che, se si rimuovono tutti i server DNS da una rete virtuale, le VM usano il servizio DNS interno di Azure. Questo servizio può risolvere kms.core.windows.net.
  
Verificare anche che il firewall guest non sia stato configurato in modo tale da bloccare i tentativi di attivazione.

1. Dopo avere verificato la corretta connettività a kms.core.windows.net, usare il comando seguente in tale prompt di Windows PowerShell con privilegi elevati. Questo comando tenta l'attivazione più volte.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Un'attivazione corretta restituisce informazioni simili alle seguenti:

**Attivazione di Windows(R), Server Datacenter Edition (12345678-1234-1234-1234-12345678) in corso… Attivazione prodotto completata.**

## <a name="faq"></a>Domande frequenti 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Windows Server 2016 è stato creato da Azure Marketplace. È necessario configurare una chiave del servizio di gestione delle chiavi per attivare Windows Server 2016? 

 
No. L'immagine in Azure Marketplace ha la chiave di configurazione del client del Servizio di gestione delle chiavi appropriata già configurata. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>L'attivazione di Windows funziona allo stesso modo indipendentemente dal fatto che la VM usi il vantaggio Azure Hybrid Use o meno? 

 
Sì. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Che cosa succede se il periodo di attivazione di Windows scade? 

 
Se il periodo di prova è scaduto e Windows non è ancora attivato, Windows Server 2008 R2 e le versioni successive di Windows visualizzeranno altre notifiche sull'attivazione. Lo sfondo del desktop rimane nero e Windows Update installerà solo gli aggiornamenti della sicurezza e quelli critici, ma non quelli facoltativi. Vedere la sezione Notifications (Notifiche) alla fine della pagina [Licensing Conditions](https://technet.microsoft.com/library/ff793403.aspx) (Condizioni di licenza).   

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
