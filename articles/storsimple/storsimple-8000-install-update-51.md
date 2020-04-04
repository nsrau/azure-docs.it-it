---
title: Installare l'aggiornamento 5.1 sul dispositivo della serie StorSimple 8000 Documenti Microsoft
description: Viene illustrato come installare StorSimple 8000 Series Update 5.1 sul dispositivo della serie StorSimple 8000.
services: storsimple
documentationcenter: NA
author: priestlg
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: v-grpr
ms.openlocfilehash: b90f0f35f908d16b0746075e638fe66769939a64
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658040"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Installare l'aggiornamento 5.1 sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come installare l'aggiornamento 5.1 in un dispositivo StorSimple che esegue una versione software precedente tramite il portale di Azure.This tutorial explains how to install Update 5.1 on a StorSimple device running an earlier software version via the Azure portal. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

L'aggiornamento 5.1 include aggiornamenti della sicurezza senza interruzioni. Gli aggiornamenti regolari o non problematici possono essere applicati tramite il portale di AzureThe non-disruptive or regular updates can be applied through the Azure portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * L'aggiornamento 5.1 è obbligatorio e deve essere installato immediatamente. Per ulteriori informazioni, vedere [Note sulla versione dell'aggiornamento 5.1](storsimple-update51-release-notes.md).
> * Si esegue una serie di controlli preliminari automatici e manuali prima dell'installazione per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure.These pre-checks are performed only if you apply the updates from the Azure portal.
> * Se si desidera installare utilizzando il metodo dell'hotfix, contattare il [supporto tecnico Microsoft](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Installare l'aggiornamento 5.1 tramite il portale di AzureInstall Update 5.1 through the Azure portal

Eseguire la procedura seguente per aggiornare il dispositivo [all'aggiornamento 5.1](storsimple-update51-release-notes.md).

> [!NOTE]
> Microsoft estrae informazioni di diagnostica aggiuntive dal dispositivo. Di conseguenza, quando il team addetto alle operazioni identifica i dispositivi che presentano problemi, sono disponibili più modi di raccogliere informazioni dal dispositivo e diagnosticare i problemi.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Per installare un aggiornamento dal portale di Azure

1. Nella pagina del servizio StorSimple, selezionare il proprio dispositivo.

    ![Selezionare il dispositivo](./media/storsimple-8000-install-update-51/update1.png)

2. Passare a **Impostazioni** > dispositivo**Aggiornamenti dispositivo**.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update-51/update2.png)

3. Se sono disponibili nuovi aggiornamenti, viene visualizzata una notifica. In alternativa, nel pannello **Aggiornamenti del dispositivo** fare clic su **Verifica la disponibilità di aggiornamenti**. Viene creato un processo per verificare la disponibilità di aggiornamenti. Al termine del processo si riceve una notifica.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update-51/update3.png)

4. Si consiglia di leggere le note sulla versione prima di installare un aggiornamento nel dispositivo. Per applicare gli aggiornamenti, fare clic su **Installa aggiornamenti**. Nel pannello **Confermare gli aggiornamenti regolari** rivedere i prerequisiti da completare prima di applicare gli aggiornamenti. Selezionare la casella di controllo per indicare che si è pronti per aggiornare il dispositivo e quindi fare clic su **Installa**.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update-51/update4.png)

5. Viene avviato un set di controlli dei prerequisiti. I controlli includono quanto segue:
   
   * **Controlli di integrità del controller** per verificare che entrambi i controller dei dispositivi siano integri e online.
   * **Controlli di integrità del componente hardware** per verificare che tutti i componenti hardware del dispositivo StorSimple siano integri.
   * **Controlla DATA 0** per verificare che DATA 0 è attivato sul dispositivo. Se questa interfaccia non è abilitata, è necessario abilitarla e riprovare.

     L'aggiornamento viene scaricato e installato solo se tutti i controlli vengono completati correttamente. Quando i controlli sono in corso si riceve una notifica. Se le verifiche preliminari hanno esito negativo, verranno indicate le cause dell'errore. Risolvere tali problemi e quindi provare a eseguire di nuovo l'operazione. Se non è possibile risolvere tali problemi in autonomia, contattare il supporto tecnico Microsoft.

7. Dopo aver completato tutte le verifiche preliminari, viene creato un processo di aggiornamento. Dopo la creazione di tale processo si riceve una notifica.
   
    ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update-51/update6.png)
   
    A questo punto, l'aggiornamento viene applicato al dispositivo.

9. Per completare l'aggiornamento possono essere necessarie alcune ore. Selezionare il processo di aggiornamento e fare clic su **Dettagli** per visualizzare i dettagli del processo in qualsiasi momento.

    ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update-51/update8.png)

     È anche possibile monitorare lo stato del processo di aggiornamento da **Impostazioni del dispositivo > Processi**. Nel pannello **Processi** è possibile visualizzare lo stato dell'aggiornamento.

     ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update-51/update7.png)

10. Al termine del processo, passare a **Impostazioni del dispositivo > Aggiornamenti del dispositivo**. La versione del software ora risulterà aggiornata.


Verificare che sul dispositivo sia in esecuzione **StorSimple 8000 Series Update 5.1 (6.3.9600.17885)**. È necessario anche modificare la **data dell'ultimo aggiornamento** .
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [versione di aggiornamento 5.1](storsimple-update51-release-notes.md).
