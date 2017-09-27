---
title: Risolvere i problemi di attivazione della macchina virtuale Windows in Azure | Microsoft Docs
description: Fornisce la procedura per risolvere i problemi di attivazione della macchina virtuale Windows in Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/26/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 77ef396e0bf75fab56bda2e76516b481d018c5b9
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Risolvere i problemi di attivazione della macchina virtuale Windows di Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se si verificano problemi durante l'attivazione della macchina virtuale (VM) Windows di Azure creata da un'immagine personalizzata, è possibile usare le informazioni contenute in questo documento per risolvere il problema. 

## <a name="symptom"></a>Sintomo

Quando si cerca di attivare una VM Windows di Azure, si riceve un messaggio di errore simile all'esempio seguente:

**Errore: 0xC004F074 Servizio gestione licenze software: impossibile attivare il computer. Impossibile contattare un servizio di gestione delle chiavi. Per altre informazioni, vedere il registro eventi applicazioni.**

## <a name="cause"></a>Causa
Si verificano in genere problemi di attivazione della VM di Azure se la VM Windows non viene configurata usando la chiave di configurazione del client del Servizio di gestione delle chiavi appropriata o la VM Windows ha un problema di connettività al Servizio di gestione delle chiavi di Azure (kms.core.windows.net, porta 1668). 

## <a name="solution"></a>Soluzione

>[!NOTE]
>Se si usano una VPN da sito a sito e il tunneling forzato, vedere [Use Azure custom routes to enable KMS activation with forced tunneling](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) (Usare le route personalizzate di Azure per abilitare l'attivazione del Servizio di gestione delle chiavi con il tunneling forzato). 
>
>Se si usa ExpressRoute ed è stata pubblicata una route predefinita, vedere [Azure VM may fail to activate over ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx) (Potrebbe non essere possibile attivare la VM di Azure tramite ExpressRoute).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Passaggio 1 Configurare la chiave di configurazione del client del Servizio di gestione delle chiavi appropriata (per Windows Server 2016 e Windows Server 2012 R2)

Per la VM creata da un'immagine personalizzata di Windows Server 2016 o Windows Server 2012 R2, è necessario configurare la chiave di configurazione del client del Servizio di gestione delle chiavi appropriata per la VM.

Questo passaggio non si applica a Windows 2012 o Windows 2008 R2. Usa la funzionalità di attivazione automatica della macchina virtuale, supportata solo da Windows Server 2016 e Windows Server 2012 R2.

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

1. Scaricare ed estrarre lo strumento [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) in una cartella locale nella VM che non viene attivata. 

2. Andare a Start, cercare Windows PowerShell, fare clic con il pulsante destro del mouse su Windows PowerShell e scegliere Esegui come amministratore.

3. Verificare che la VM sia configurata per usare il server di gestione delle chiavi di Azure corretto. A tale scopo, usare il comando seguente:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Il comando restituirà: nome del computer del Servizio di gestione delle chiavi impostato su kms.core.windows.net:1688.

4. Usando Psping verificare di avere la connettività al server di gestione delle chiavi. Passare alla cartella in cui si è estratto il download Pstools.zip e quindi eseguire quanto segue:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Nelle righe dalla seconda all'ultima dell'output verificare che sia visualizzato: Sent = 4, Received = 4, Lost = 0 (0% loss).

  Se Lost è maggiore di 0 (zero), la VM non ha la connettività al server di gestione delle chiavi. In questo caso, se la VM è in una rete virtuale ed è specificato un server DNS personalizzato, è necessario verificare che il server DNS possa risolvere kms.core.windows.net. In alternativa, sostituire il server DNS con uno che risolve kms.core.windows.net.

  Tenere presente che, se si rimuovono tutti i server DNS da una rete virtuale, le VM usano il servizio DNS interno di Azure. Questo servizio può risolvere kms.core.windows.net.
  
Verificare anche che il firewall guest non sia stato configurato in modo tale da bloccare i tentativi di attivazione.

5. Dopo avere verificato la corretta connettività a kms.core.windows.net, usare il comando seguente in tale prompt di Windows PowerShell con privilegi elevati. Questo comando tenta l'attivazione più volte.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Un'attivazione corretta restituisce informazioni simili alle seguenti:

**Attivazione di Windows(R), Server Datacenter Edition (12345678-1234-1234-1234-12345678) in corso… Attivazione prodotto completata.**

## <a name="faq"></a>Domande frequenti 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Windows Server 2016 è stato creato da Azure Marketplace. È necessario configurare una chiave del servizio di gestione delle chiavi per attivare Windows Server 2016? 
 
No. L'immagine in Azure Marketplace ha la chiave di configurazione del client del Servizio di gestione delle chiavi appropriata già configurata. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>L'attivazione di Windows funziona allo stesso modo indipendentemente dal fatto che la VM usi il vantaggio Azure Hybrid Use o meno? 
 
Sì. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Che cosa succede se il periodo di attivazione di Windows scade? 
 
Se il periodo di prova è scaduto e Windows non è ancora attivato, Windows Server 2008 R2 e le versioni successive di Windows visualizzeranno altre notifiche sull'attivazione. Lo sfondo del desktop rimane nero e Windows Update installerà solo gli aggiornamenti della sicurezza e quelli critici, ma non quelli facoltativi. Vedere la sezione Notifications (Notifiche) alla fine della pagina [Licensing Conditions](http://technet.microsoft.com/en-us/library/ff793403.aspx) (Condizioni di licenza).   

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
