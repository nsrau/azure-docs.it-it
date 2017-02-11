---
title: Modificare le impostazioni dei DATI 0 in un dispositivo StorSimple | Microsoft Docs
description: Informazioni su come utilizzare Windows PowerShell per StorSimple per riconfigurare l&quot;interfaccia di rete DATI 0 sul dispositivo StorSimple
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 58e3d509-f425-4a7f-b650-d496a7c85193
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3a47ff1eed220cede820e8698c3384300e94688d


---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Modificare le impostazioni dell'interfaccia di rete DATI 0 sul dispositivo StorSimple
## <a name="overview"></a>Overview
Il dispositivo Microsoft Azure StorSimple ha sei interfacce di rete, da DATI 0 a DATI 5. L'interfaccia DATI 0 è sempre configurata tramite l'interfaccia di Windows PowerShell o la console seriale ed è automaticamente abilitata per il cloud. Si noti che non è possibile configurare l'interfaccia di rete DATI 0 con il portale di Azure classico. 

L'interfaccia DATI 0 viene inizialmente configurata tramite l'installazione guidata durante la distribuzione iniziale del dispositivo StorSimple. Quando il dispositivo è in modalità operativa, potrebbe essere necessario riconfigurare le impostazioni di DATI 0. In questa esercitazione sono disponibili due metodi per modificare le impostazioni di rete di DATI 0, entrambi tramite Windows PowerShell per StorSimple.

Dopo aver letto questa esercitazione, si sarà in grado di:

* Modificare l'impostazione di rete di DATI 0 tramite la configurazione guidata
* Modificare le impostazioni di rete di DATI 0 con il cmdlet `Set-HcsNetInterface`

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificare le impostazioni di rete di DATI 0 tramite la configurazione guidata
È possibile riconfigurare le impostazioni di rete di DATI 0 connettendosi all'interfaccia di Windows PowerShell del dispositivo StorSimple e avviando una sessione di configurazione guidata. Eseguire i passaggi seguenti per modificare le impostazioni di DATI 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Per modificare le impostazioni di rete di DATI 0 tramite la configurazione guidata
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è `Password1`.
2. Al prompt dei comandi digitare:
   
    `Invoke-HcsSetupWizard`
3. Verrà visualizzata una procedura guidata per configurare l'interfaccia DATI 0 del dispositivo. Fornire nuovi valori per l'indirizzo IP, il gateway e la netmask.

> [!NOTE]
> Gli IP fissi dei controller dovranno essere riconfigurati tramite la pagina **Configura** del dispositivo StorSimple nel portale di Azure classico. Per ulteriori informazioni, andare a [Modificare le interfacce di rete](storsimple-modify-device-config.md#modify-network-interfaces).
> 
> 

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificare le impostazioni di rete di DATI 0 tramite il cmdlet Set-HcsNetInterface
Un modo alternativo per riconfigurare l'interfaccia di rete DATI 0 consiste nell'uso del cmdlet `Set-HcsNetInterface`. Il cmdlet viene eseguito dall'interfaccia di Windows PowerShell del dispositivo StorSimple. Quando si usa questa procedura, gli indirizzi IP fissi del controller possono essere anche configurati in questa posizione. Eseguire i passaggi seguenti per modificare le impostazioni di DATI 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Per modificare le impostazioni di rete di DATI 0 tramite il cmdlet Set-HcsNetInterface
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la password di amministratore del dispositivo. La password predefinita è `Password1`.
2. Al prompt dei comandi digitare:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Tra parentesi acute digitare i seguenti valori per DATA 0:
   
   * Indirizzo IPv4
   * Gateway IPv4
   * Subnet mask IPv4
   * Indirizzo IPv4 fisso per controller 0
   * Indirizzo IPv4 fisso per controller 1
     
     Per altre informazioni su come usare questo cmdlet, vedere le [informazioni di riferimento sui cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Passaggi successivi
* Per configurare le interfacce di rete diverse da DATA 0, è possibile utilizzare la [pagina di configurazione nel portale di Azure classico](storsimple-modify-device-config.md). 
* Se si riscontrano problemi durante la configurazione delle interfacce di rete, fare riferimento a [Risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md).




<!--HONumber=Nov16_HO3-->


