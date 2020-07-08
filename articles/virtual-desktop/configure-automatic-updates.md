---
title: Configurare Microsoft endpoint Configuration Manager-Azure
description: Come configurare Microsoft endpoint Configuration Manager per distribuire gli aggiornamenti software in più sessioni di Windows 10 Enterprise in un desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: 100e269bb995f86d0721cd6de28cd4b933b58ecd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204388"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Configurare Configuration Manager endpoint Microsoft

Questo articolo illustra come configurare Microsoft endpoint Configuration Manager per applicare automaticamente gli aggiornamenti a un host di desktop virtuale Windows che esegue la multisessione Enterprise di Windows 10.

## <a name="prerequisites"></a>Prerequisiti

Per configurare questa impostazione, sono necessari gli elementi seguenti:

   - Assicurarsi di aver installato Microsoft endpoint Configuration Manager Agent nelle macchine virtuali.
   - Verificare che la versione di Microsoft endpoint Configuration Manager sia almeno al livello di ramo 1906. Per ottenere risultati ottimali, usare il livello di ramo 1910 o superiore.

## <a name="configure-the-software-update-point"></a>Configurare il punto di aggiornamento software

Per ricevere gli aggiornamenti per la multisessione Enterprise di Windows 10, è necessario abilitare Windows Server, versione 1903 e successive come prodotto all'interno di Microsoft endpoint Configuration Manager. Questa impostazione del prodotto si applica anche se si usa il servizio Windows Server Update per distribuire gli aggiornamenti ai sistemi.

Per ricevere gli aggiornamenti:

1. Aprire Microsoft endpoint Configuration Manager e selezionare **siti**.
2. Selezionare **Configura componenti del sito**.
3. Selezionare **punto di aggiornamento software** nel menu a discesa.
4. Selezionare la scheda **Prodotti**.
5. Selezionare la casella di controllo che indica **Windows Server, versione 1903 e successive**.
6. Passare a **raccolta software**  >  **Panoramica**  >  **aggiornamenti software**  >  **tutti gli aggiornamenti software** e selezionare **Sincronizza aggiornamenti software**.
7. Controllare il file wsyncmgr. log nei **file di programma**  >  **Microsoft Configuration Manager**  >  **logs** per assicurarsi che le modifiche siano state salvate. La sincronizzazione degli aggiornamenti potrebbe richiedere alcuni minuti.

## <a name="create-a-query-based-collection"></a>Creare una raccolta basata su query

Per creare una raccolta di macchine virtuali con più sessioni Enterprise di Windows 10, è possibile usare una raccolta basata su query per identificare lo SKU del sistema operativo specifico.

Per creare una raccolta:

1. Selezionare **asset e conformità**.
2. Passare a **Panoramica**  >  **Raccolte dispositivi** e fare clic con il pulsante destro del mouse su **Raccolte dispositivi** e selezionare **Crea raccolta dispositivi** dal menu a discesa.
3. Nella scheda **generale** del menu visualizzato immettere un nome che descriva la raccolta nel campo **nome** . Nel campo **Comment** è possibile specificare informazioni aggiuntive che descrivono l'elemento della raccolta. Nella **raccolta di limitazione**definire i computer inclusi nella query di raccolta.
4. Nella scheda **regole di appartenenza** aggiungere una regola per la query selezionando **Aggiungi regola**, quindi selezionando **regola di query**.
5. In **Proprietà regola di query**immettere un nome per la regola, quindi definire i parametri della regola selezionando **Modifica istruzione query**.
6. Selezionare **Mostra istruzione query**.
7. Nell'istruzione immettere la stringa seguente:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Selezionare **OK** per creare la raccolta.
9. Per verificare se la raccolta è stata creata correttamente, passare a **asset e conformità**  >  **Panoramica**  >  **Raccolte dispositivi**.
