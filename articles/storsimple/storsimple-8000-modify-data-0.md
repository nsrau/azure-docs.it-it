---
title: Modificare le impostazioni di DATA 0 in un dispositivo StorSimple serie 8000 | Microsoft Docs
description: Informazioni su come utilizzare Windows PowerShell per StorSimple per riconfigurare l'interfaccia di rete DATI 0 sul dispositivo StorSimple
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modificare le impostazioni dell'interfaccia di rete DATA 0 in un dispositivo StorSimple serie 8000

## <a name="overview"></a>Panoramica

Il dispositivo Microsoft Azure StorSimple ha sei interfacce di rete, da DATI 0 a DATI 5. L'interfaccia DATI 0 è sempre configurata tramite l'interfaccia di Windows PowerShell o la console seriale ed è automaticamente abilitata per il cloud. Si noti che non è possibile configurare l'interfaccia di rete DATA 0 con il portale di Azure.

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
3. Verrà visualizzata una procedura guidata per configurare l'interfaccia DATA 0 del dispositivo. Fornire nuovi valori per l'indirizzo IP, il gateway e la netmask.

> [!NOTE]
> Gli IP fissi dei controller dovranno essere riconfigurati tramite il pannello **Impostazioni di rete** del dispositivo StorSimple nel portale di Azure. Per ulteriori informazioni, andare a [Modificare le interfacce di rete](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificare le impostazioni di rete di DATI 0 tramite il cmdlet Set-HcsNetInterface
Un modo alternativo per riconfigurare l'interfaccia di rete DATI 0 consiste nell'utilizzo del cmdlet `Set-HcsNetInterface` . Il cmdlet viene eseguito dall'interfaccia di Windows PowerShell del dispositivo StorSimple. Quando si usa questa procedura, gli indirizzi IP fissi del controller possono essere anche configurati in questa posizione. Eseguire i passaggi seguenti per modificare le impostazioni di DATI 0: 

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
* Per configurare interfacce di rete diverse da DATA 0, vedere l'articolo su come [configurare le impostazioni di rete nel portale di Azure](storsimple-8000-modify-device-config.md). 
* Se si riscontrano problemi durante la configurazione delle interfacce di rete, fare riferimento a [Risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md).

