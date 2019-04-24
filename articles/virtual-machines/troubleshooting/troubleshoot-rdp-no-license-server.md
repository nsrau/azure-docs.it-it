---
title: Il server licenze Desktop remoto non è disponibile durante la connessione a una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi di RDP quando non è disponibile un server licenze Desktop remoto | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318953"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Il server licenze Desktop remoto non è disponibile durante la connessione a una macchina virtuale di Azure

Questo articolo illustra come risolvere il problema che si verifica quando non è possibile connettersi a una macchina virtuale (VM) di Azure in quanto non è disponibile un server licenze Desktop remoto per il rilascio di una licenza.

## <a name="symptoms"></a>Sintomi

Quando si prova a connettersi a una macchina virtuale, si verificano gli scenari seguenti:

- Lo screenshot della macchina virtuale indica che il sistema operativo è stato caricato completamente ed è in attesa delle credenziali.
- Quando si prova a stabilire una connessione Microsoft RDP (Remote Desktop Protocol), vengono visualizzati gli errori seguenti:

  - La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.

  - Non sono disponibili server licenze Desktop remoto. Servizi Desktop remoto smetterà di funzionare perché il periodo di prova è scaduto e non è stato contattato un server licenze Windows Server 2008 valido. Selezionare questo messaggio per aprire Configurazione server host sessione Desktop remoto per usare lo strumento di diagnosi delle licenze.

È tuttavia possibile connettersi normalmente alla macchina virtuale tramite una sessione amministrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Questo problema si verifica se non è disponibile un server licenze Desktop remoto per il rilascio di una licenza per l'avvio di una sessione remota. Il problema può essere causato da diversi scenari, anche se è stato configurato un ruolo Host sessione Desktop remoto nella macchina virtuale:

- Nell'ambiente non è mai stato presente un ruolo Servizio licenze Desktop remoto e il periodo di prova (180 giorni) è scaduto.
- Nell'ambiente è stata installata una licenza di Desktop remoto che non è mai stata attivata.
- Una licenza di Desktop remoto nell'ambiente non ha licenze CAL (Client Access License) inserite per configurare la connessione.
- Nell'ambiente è stata installata una licenza di Desktop remoto. Sono disponibili licenze CAL che tuttavia non sono state configurate correttamente.
- Una licenza di Desktop remoto ha licenze CAL ed è stata attivata. Alcuni altri problemi nel server licenze Desktop remoto impediscono tuttavia il rilascio di licenze nell'ambiente.

## <a name="solution"></a>Soluzione

Per risolvere il problema, [eseguire il backup del disco del sistema operativo](../windows/snapshot-copy-managed-disk.md) e seguire questi passaggi:

1. Connettersi alla macchina virtuale tramite una sessione amministrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se non è possibile connettersi alla macchina virtuale tramite una sessione amministrativa, usare la [console seriale della macchina virtuale](serial-console-windows.md) per accedere alla macchina virtuale come indicato di seguito:

    1. Accedere alla console seriale selezionando **Supporto e risoluzione dei problemi** > **Console seriale (anteprima)**. Se la funzionalità è abilitata nella macchina virtuale, è possibile connettere correttamente la macchina virtuale.

    2. Creare un nuovo canale per un'istanza CMD. Immettere **CMD** per avviare il canale e ottenere il relativo nome.

    3. Passare al canale che esegue l'istanza di CMD. In questo caso, dovrebbe essere il canale 1:

       ```
       ch -si 1
       ```

    4. Premere di nuovo **INVIO** e digitare un nome utente e una password validi, ID locale o di dominio, per la macchina virtuale.

2. Controllare se nella macchina virtuale è abilitato un ruolo Host sessione Desktop remoto. Se il ruolo è abilitato, assicurarsi che funzioni correttamente. Aprire un'istanza di CMD con privilegi elevati e seguire questa procedura:

    1. Usare il comando seguente per controllare lo stato del ruolo Host sessione Desktop remoto:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se il comando restituisce un valore pari a 0, significa che il ruolo è disabilitato ed è possibile andare al passaggio 3.

    2. Usare il comando seguente per controllare i criteri e riconfigurarli in base alle esigenze:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se **LicensingMode** è impostato su un valore diverso da 4 (per utente), impostarlo su 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se il valore di **SpecifiedLicenseServers** non è presente o include informazioni sul server licenze non corrette, modificarlo come indicato di seguito:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Dopo avere apportato le modifiche al Registro di sistema, riavviare la macchina virtuale.

    4. Se non si dispone di licenze CAL, rimuovere il ruolo Host sessione Desktop remoto. A questo punto, il protocollo RDP verrà reimpostato allo stato normale. Consente solo due connessioni RDP simultanee alla macchina virtuale:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se la macchina virtuale ha il ruolo Servizio licenze Desktop remoto e tale ruolo non è in uso, è possibile rimuoverlo:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Assicurarsi che la macchina virtuale possa connettersi al server licenze Desktop remoto. È possibile testare la connettività alla porta 135 tra la macchina virtuale e il server licenze: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se nell'ambiente non è presente alcun server licenze Desktop remoto e se ne vuole uno, è possibile [installare un ruolo Servizio licenze Desktop remoto](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Quindi [configurare le licenze di Servizi Desktop remoto](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Se un server licenze Desktop remoto è configurato e integro, assicurarsi che sia attivato con le licenze CAL.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), per ottenere la risoluzione del problema.
