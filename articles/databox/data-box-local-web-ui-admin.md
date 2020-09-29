---
title: Amministrare Azure Data Box/Azure Data Box Heavy usando l'interfaccia utente Web locale
description: Viene descritto come usare l'interfaccia utente Web locale per amministrare i dispositivi Data Box e Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: 8455fafe9ce2465df450e9556e8b2442b01e4e23
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449713"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Usare l'interfaccia utente Web locale per amministrare il Data Box e Data Box Heavy

Questo articolo descrive alcune delle attività di configurazione e gestione eseguite sui dispositivi Data Box e Data Box Heavy. È possibile gestire i dispositivi Data Box e Data Box Heavy tramite l'interfaccia utente di portale di Azure e l'interfaccia utente Web locale per il dispositivo. Questo articolo è incentrato sulle attività eseguite usando l'interfaccia utente Web locale.

L'interfaccia utente Web locale per il Data Box e per Data Box Heavy viene usata per la configurazione iniziale del dispositivo. È anche possibile usare l'interfaccia utente Web locale per arrestare o riavviare il dispositivo, eseguire test diagnostici, aggiornare il software, visualizzare i log di copia e generare un pacchetto di log per supporto tecnico Microsoft. In un dispositivo Data Box Heavy con due nodi indipendenti, è possibile accedere a due interfacce utente Web locali separate corrispondenti a ogni nodo del dispositivo.

L'articolo include le esercitazioni seguenti:

- Creare un pacchetto di supporto
- Arrestare o riavviare il dispositivo
- Scarica la fattura del materiale (BOM) o i file manifesto
- Visualizzare la capacità disponibile del dispositivo
- Ignorare la convalida di checksum

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>Creare un pacchetto per il supporto

Se si verificano problemi al dispositivo, è possibile creare un pacchetto di supporto dai log di sistema. Il supporto tecnico Microsoft usa questo pacchetto per risolvere il problema. Per generare un pacchetto per il supporto, seguire questa procedura:

1. Nell'interfaccia utente Web locale passare a **Contatta il supporto tecnico** e selezionare **Crea pacchetto**per il supporto.

    ![Creare un pacchetto per il supporto 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Viene raccolto un pacchetto di supporto. Il completamento dell'operazione richiede alcuni minuti.

    ![Creare un pacchetto per il supporto 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Al termine della creazione del pacchetto di supporto, selezionare **Scarica pacchetto**per il supporto.

    ![Creare un pacchetto per il supporto 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Individuare e scegliere il percorso di download. Aprire la cartella per visualizzarne il contenuto.

    ![Creare un pacchetto per il supporto 5](media/data-box-local-web-ui-admin/create-support-package-5.png)

## <a name="shut-down-or-restart-your-device"></a>Arrestare o riavviare il dispositivo

È possibile arrestare o riavviare il dispositivo usando l'interfaccia utente Web locale. Prima di riavviare, si consiglia di portare offline le condivisioni sull'host e quindi il dispositivo. Questa operazione riduce al minimo eventuali possibili danni ai dati. Assicurarsi che la copia dei dati non sia in corso quando si arresta il dispositivo.

Per arrestare il dispositivo, seguire questa procedura.

1. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
2. Selezionare **Arresta**.

    ![Arrestare Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Quando viene richiesta la conferma, selezionare **OK** per continuare.

    ![Arrestare Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Una volta arrestato il dispositivo, usare il pulsante di alimentazione sul pannello anteriore per accendere il dispositivo.

Per riavviare Data Box seguire questa procedura.

1. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
2. Selezionare **Restart** (Riavvia).

    ![Riavviare Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Quando viene richiesta la conferma, selezionare **OK** per continuare.

   Il dispositivo si arresta e quindi si riavvia.

## <a name="download-bom-or-manifest-files"></a>Scaricare la distinta base o i file manifesto

Il BOM o i file manifesto contengono l'elenco dei file copiati nel Data Box o Data Box Heavy. Questi file vengono generati per un ordine di importazione quando si prepara il dispositivo per la spedizione.

Prima di iniziare, seguire questa procedura per scaricare i file BOM o manifest per l'ordine di importazione:

1. Passare all'interfaccia utente Web locale per il dispositivo. Verificare che il dispositivo abbia completato il **prepara per la spedizione** passaggio. Al termine della preparazione, lo stato visualizzato per il dispositivo è **Pronto per la spedizione**.

    ![Dispositivo pronto per la spedizione](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Selezionare **Scarica l'elenco dei file** per scaricare l'elenco dei file copiati nel data box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. In Esplora file si noterà che vengono generati elenchi distinti di file a seconda del protocollo usato per connettersi al dispositivo e al tipo di archiviazione di Azure usato.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![File per tipo di archiviazione e protocollo di connessione](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   Nella tabella seguente i nomi file sono associati al tipo di archiviazione di Azure e al protocollo di connessione usato.

    |Nome file  |Tipo di archiviazione di Azure  |Protocollo di connessione usato |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |BLOB in blocchi         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |BLOB di pagine         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |File di Azure         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |BLOB di pagine         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |BLOB in blocchi         |REST         |

Usare questo elenco per verificare i file caricati nell'account di archiviazione di Azure dopo la restituzione del Data Box al data center di Azure. Di seguito è riportato un esempio di file manifesto.

> [!NOTE]
> In una Data Box Heavy sono presenti due set di file (file BOM) corrispondenti ai due nodi del dispositivo.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Questo file contiene l'elenco di tutti i file copiati nella Data Box o Data Box Heavy. In questo file il valore *crc64* è correlato al checksum generato per il file corrispondente.

## <a name="view-available-capacity-of-the-device"></a>Visualizzare la capacità disponibile del dispositivo

È possibile usare il dashboard del dispositivo per visualizzare la capacità disponibile e usata nel dispositivo.

1. Nell'interfaccia utente Web locale passare a **Visualizza dashboard**.
2. Sotto **Connetti e copia** viene visualizzato lo spazio libero e occupato sul dispositivo.

    ![Visualizzare la capacità disponibile](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Ignorare la convalida di checksum

I checksum vengono generati per i dati per impostazione predefinita quando si prepara la spedizione. In alcuni casi rari, a seconda del tipo di dati (file di piccole dimensioni), le prestazioni potrebbero essere lente. In questi casi, è possibile ignorare i checksum.

Il calcolo del checksum durante la preparazione alla spedizione viene eseguito solo per gli ordini di importazione e non per gli ordini di esportazione. 

È consigliabile non disabilitare i checksum, a meno che le prestazioni non subiscano un notevole peggioramento.

1. Nell'angolo in alto a destra dell'interfaccia utente Web locale del dispositivo passare a **Impostazioni**.

    ![Disabilitare i checksum](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Disabilitare** la convalida dei checksum
3. Selezionare **Applica**.

> [!NOTE]
> L'opzione di calcolo ignora checksum è disponibile solo quando il Azure Data Box è sbloccato. Questa opzione non viene visualizzata quando il dispositivo è bloccato.

## <a name="enable-smb-signing"></a>Abilita firma SMB

La firma SMB (Server Message Block) è una funzionalità tramite la quale le comunicazioni che usano SMB possono essere firmate digitalmente a livello di pacchetto. Questa firma impedisce gli attacchi che modificano i pacchetti SMB in transito.

Per ulteriori informazioni sulla firma SMB, vedere la pagina relativa alla [Panoramica della firma di Server Message Block](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Per abilitare la firma SMB nel dispositivo Azure:

1. Nell'angolo in alto a destra dell'interfaccia utente Web locale del dispositivo selezionare **Impostazioni**.

    ![Aprire le impostazioni](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Abilita** Firma SMB.

    ![Abilita firma SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Selezionare **Applica**.
4. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
5. Selezionare **Restart** (Riavvia).

## <a name="enable-backup-operator-privileges"></a>Abilitare i privilegi dell'operatore di backup

Per impostazione predefinita, gli utenti dell'interfaccia utente Web hanno privilegi di operatore di backup per le condivisioni SMB. Se non si vuole, usare **Abilita i privilegi di operatore indietro** per disabilitare o abilitare i privilegi.

Per informazioni, vedere Backup Operators in [Active Directory Security Groups](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Per abilitare i privilegi dell'operatore di backup nel dispositivo Azure:

1. Nell'angolo in alto a destra dell'interfaccia utente Web locale del dispositivo selezionare **Impostazioni**.

   ![Apri impostazioni Data Box](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Abilita** Privilegi dell'operatore di backup.

   ![Abilitare i privilegi dell'operatore di backup](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Selezionare Applica**.
4. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
5. Selezionare **Restart** (Riavvia).

## <a name="enable-acls-for-azure-files"></a>Abilita ACL per File di Azure

Per impostazione predefinita, i metadati dei file vengono trasferiti quando gli utenti caricano i dati tramite SMB nel Data Box. I metadati includono elenchi di controllo di accesso (ACL), attributi di file e timestamp. Se non si vuole, usare **ACL per file di Azure** per disabilitare o abilitare questa funzionalità.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Per trasferire i metadati con i file, è necessario essere un operatore di backup. Quando si usa questa funzionalità, assicurarsi che gli utenti locali dell'interfaccia utente Web siano operatori di backup. Vedere [abilitare i privilegi dell'operatore di backup](#enable-backup-operator-privileges).

Per abilitare il trasferimento degli ACL per file di Azure:

1. Nell'angolo in alto a destra dell'interfaccia utente Web locale del dispositivo selezionare **Impostazioni**.

    ![Apri impostazioni Data Box](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Abilita** ACL per file di Azure.

     ![Abilitare gli ACL per file di Azure](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Selezionare **Applica**.
4. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
5. Selezionare **Restart** (Riavvia).

## <a name="enable-tls-11"></a>Abilitare TLS 1,1

Per impostazione predefinita, Azure Data Box usa Transport Layer Security (TLS) 1,2 per la crittografia perché è più sicuro di TSL 1,1. Tuttavia, se l'utente o i client utilizzano un browser per accedere ai dati che non supportano TLS 1,2, è possibile abilitare TLS 1,1.

Per altre informazioni relative a TLS, vedere [Azure Data Box Gateway sicurezza](../databox-online/data-box-gateway-security.md).

Per abilitare TLS 1,1 nel dispositivo Azure:

1. Nell'angolo in alto a destra dell'interfaccia utente Web locale del dispositivo selezionare **Impostazioni**.

    ![Apri impostazioni Data Box](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Abilita** TLS 1,1.

    ![Abilitare TLS 1,1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Selezionare **Applica**.
4. Nell'interfaccia utente Web locale passare a **Arresta o riavvia**.
5. Selezionare **Restart** (Riavvia).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire il data box e data box Heavy tramite il portale di Azure](data-box-portal-admin.md).
