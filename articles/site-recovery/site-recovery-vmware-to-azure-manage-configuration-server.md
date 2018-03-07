---
title: Gestire il server di configurazione per il ripristino di emergenza di VMware con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come gestire un server di configurazione esistente per il ripristino di emergenza di VMware in Azure con Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 80426b076481aaf2001644498da8cdce6f0311ab
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gestire il server di configurazione per le macchine virtuali VMware

Un server di configurazione locale viene impostato quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure. Il server di configurazione coordina le comunicazioni tra VMware locale e Azure e gestisce la replica dei dati. Questo articolo riepiloga le attività comuni per la gestione del server di configurazione dopo che è stato distribuito.


## <a name="modify-vmware-settings"></a>Modificare le impostazioni di VMware

Modificare le impostazioni per il server VMware a cui si connette il server di configurazione.

1. Accedere al computer che esegue il server di configurazione.
2. Avviare Gestione configurazione di Azure Site Recovery dal collegamento sul desktop. In alternativa, aprire [questo collegamento](https://configuration-server-name/IP:44315).
3. Selezionare **Gestisci credenziali del server vCenter/vSphere ESXi** e quindi eseguire le operazioni seguenti:

    * Per associare un server VMware diverso al server di configurazione, selezionare **Aggiungi server vCenter/vSphere ESXi**. Immettere i dettagli del server.

    * Per aggiornare le credenziali usate per connettersi al server VMware per l'individuazione automatica delle macchine virtuali VMware, selezionare **Modifica**. Immettere le nuove credenziali e quindi selezionare **OK**.

    ![Modificare VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificare le credenziali per l'installazione del servizio Mobility

Modificare le credenziali usate per installare automaticamente il servizio Mobility nelle macchine virtuali VMware abilitate per la replica.

1. Accedere al computer che esegue il server di configurazione.
2. Avviare Gestione configurazione di Site Recovery dal collegamento sul desktop. In alternativa, aprire [questo collegamento](https://configuration-server-name/IP:44315).
3. Selezionare **Gestisci credenziali della macchina virtuale** e immettere le nuove credenziali, quindi selezionare **OK** per aggiornare le impostazioni.

    ![Modificare le credenziali del servizio Mobility](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modificare le impostazioni proxy

Modificare le impostazioni proxy usate dal computer server di configurazione per l'accesso Internet ad Azure. Se si ha un computer server di elaborazione, oltre al server di elaborazione predefinito in esecuzione nel computer server di configurazione, modificare le impostazioni in entrambi i computer.

1. Accedere al computer che esegue il server di configurazione.
2. Avviare Gestione configurazione di Site Recovery dal collegamento sul desktop. In alternativa, aprire [questo collegamento](https://configuration-server-name/IP:44315).
3. Selezionare **Gestisci connettività** e aggiornare i valori proxy, quindi selezionare **Salva** per aggiornare le impostazioni.

## <a name="add-a-network-adapter"></a>Aggiungere una scheda di rete

Il modello OVF (Open Virtualization Format) distribuisce la macchina virtuale del server di configurazione con una sola scheda di rete. È possibile [aggiungere un'altra scheda alla macchina virtuale](how-to-deploy-configuration-server.md#add-an-additional-adapter), ma è necessario aggiungerla prima di registrare il server di configurazione nell'insieme di credenziali.

Per aggiungere una scheda dopo aver registrato il server di configurazione nell'insieme di credenziali, aggiungere la scheda nelle proprietà della macchina virtuale. Registrare quindi il server nell'insieme di credenziali.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrare di nuovo un server di configurazione nello stesso insieme di credenziali

È possibile registrare nuovamente il server di configurazione nello stesso insieme di credenziali, se necessario. Se si ha un altro computer server di elaborazione, oltre al server di elaborazione predefinito in esecuzione nel computer server di configurazione, registrare nuovamente entrambi i computer.


  1. Nell'insieme di credenziali aprire **Gestisci** > **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**.
  2. In **Server** selezionare **Scarica chiave di registrazione** per scaricare il file di credenziali dell'insieme di credenziali.
  3. Accedere al computer del server di configurazione.
  4. In **%ProgramData%\ASR\home\svagent\bin** aprire **cspsconfigtool.exe**.
  5. Nella scheda **Vault Registration** (Registrazione dell'insieme di credenziali) selezionare **Sfoglia** e individuare il file di credenziali dell'insieme di credenziali scaricato.
  6. Se necessario, fornire i dettagli del server proxy. Selezionare quindi **Register** (Registra).
  7. Aprire una finestra dei comandi di PowerShell per amministratore ed eseguire il comando seguente:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Aggiornare il server di configurazione

Per aggiornare il server di configurazione si eseguono aggiornamenti cumulativi. È possibile applicare gli aggiornamenti a un massimo di N-4 versioni. Ad esempio: 

- Se si esegue la versione 9.7, 9.8, 9.9 o 9.10, è possibile eseguire l'aggiornamento direttamente alla versione 9.11.
- Se si esegue la versione 9.6 o una versione precedente e si desidera eseguire l'aggiornamento alla 9.11, è necessario prima eseguire l'aggiornamento alla versione 9.7 e poi alla 9.11.

I collegamenti agli aggiornamenti cumulativi per tutte le versioni del server di configurazione sono disponibili nella [pagina wiki degli aggiornamenti](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Per aggiornare il server, seguire questa procedura:

1. Scaricare il file del programma di installazione dell'aggiornamento nel server di configurazione.
2. Fare doppio clic per eseguire il programma di installazione.
3. Il programma di installazione rileva la versione corrente in esecuzione nel computer.
4. Selezionare **OK** per confermare ed eseguire l'aggiornamento. 


## <a name="delete-or-unregister-a-configuration-server"></a>Eliminare o annullare la registrazione di un server di configurazione

1. [Disabilitare la protezione](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) di tutte le macchine virtuali nel server di configurazione.
2. [Annullare l'associazione](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) ed [eliminare](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) tutti i criteri di replica dal server di configurazione.
3. [Eliminare](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) tutti i server vCenter/host vSphere associati al server di configurazione.
4. Nell'insieme di credenziali aprire **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**.
5. Selezionare il server di configurazione che si vuole rimuovere, quindi nella pagina **Dettagli** selezionare **Elimina**.

    ![Eliminare il server di configurazione](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Eseguire l'eliminazione con PowerShell

Facoltativamente, è possibile eliminare il server di configurazione usando PowerShell.

1. [Installare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) il modulo Azure PowerShell.
2. Accedere al proprio account di Azure con il seguente comando:
    
    `Login-AzureRmAccount`
3. Selezionare la sottoscrizione dell'insieme di credenziali.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Impostare il contesto dell'insieme di credenziali.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recuperare il server di configurazione.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminare il server di configurazione.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> È possibile usare l'opzione **-Force** in Remove-AzureRmSiteRecoveryFabric per l'eliminazione forzata del server di configurazione.
 


## <a name="renew-ssl-certificates"></a>Rinnovare i certificati SSL

Il server di configurazione include un server Web integrato che orchestra le attività del servizio Mobility, dei server di elaborazione e dei server di destinazione master connessi. Il server Web usa un certificato SSL per autenticare i client. Il certificato scade dopo tre anni e può essere rinnovato in qualsiasi momento.

### <a name="check-expiry"></a>Controllare la scadenza

Per le distribuzioni del server di configurazione precedenti maggio 2016, la scadenza del certificato è stata impostata su un anno. Se si ha un certificato che sta per scadere, si verifica quanto segue:

- Quando mancano due mesi o meno alla data di scadenza, il servizio avvia l'invio di notifiche nel portale e tramite posta elettronica (se è stata effettuata la sottoscrizione alle notifiche di Site Recovery).
- Un banner di notifica viene visualizzato nella pagina delle risorse dell'insieme di credenziali. Per altre informazioni, selezionare il banner.
- Se viene visualizzato il pulsante **Aggiorna ora**, alcuni componenti nell'ambiente non sono stati aggiornati alla versione 9.4.xxxx.x o versioni successive. Aggiornare i componenti prima di rinnovare il certificato. Non è possibile eseguire il rinnovo per le versioni precedenti.

### <a name="renew-the-certificate"></a>Rinnovare il certificato

1. Nell'insieme di credenziali aprire **Site Recovery Infrastructure** (Infrastruttura di Site Recovery) > **Server di configurazione**. Selezionare il server di configurazione richiesto.
2. La data di scadenza viene visualizzata in **Integrità del server di configurazione**.
3. Selezionare **Rinnova certificati**. 


## <a name="next-steps"></a>Passaggi successivi

Rivedere le esercitazioni per la configurazione del ripristino di emergenza delle [macchine virtuali VMware](tutorial-vmware-to-azure.md) in Azure.
