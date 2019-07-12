---
title: Risoluzione dei problemi di Azure Site Recovery per gli errori di replica da Azure ad Azure | Microsoft Docs
description: Risoluzione dei problemi e degli errori che si verificano quando si esegue la replica di macchine virtuali di Azure per il ripristino di emergenza
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 1e0450554597d99aa99d6df51f22bfc90c0d92ad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798571"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Risolvere i problemi di replica delle VM da Azure ad Azure

Questo articolo descrive i problemi comuni in Azure Site Recovery quando si eseguono la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra e illustra come risolverli. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Elenco degli errori
- **[Problemi di quota delle risorse di Azure (codice errore 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Certificati radice attendibili (codice errore 151066)](#trusted-root-certificates-error-code-151066)**
- **[Connettività in uscita per Site Recovery (codice errore 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemi di quota delle risorse di Azure (codice errore 150097)
È necessario che la sottoscrizione sia abilitata per la creazione di VM di Azure nell'area di destinazione che si prevede di usare come area per il ripristino di emergenza. Per la sottoscrizione deve inoltre essere abilitata una quota sufficiente per la creazione di VM di dimensioni specifiche. Per impostazione predefinita, Site Recovery usa, per la VM di destinazione, le stesse dimensioni della VM di origine. Se le dimensioni corrispondenti non sono disponibili, vengono selezionate automaticamente le dimensioni più vicine possibili. Se non ci sono dimensioni corrispondenti che supportano la configurazione della VM di origine, viene visualizzato il messaggio di errore seguente:

**Codice errore** | **Possibili cause** | **Consiglio**
--- | --- | ---
150097<br></br>**Messaggio**: Replication couldn't be enabled for the virtual machine VmName (Non è stato possibile abilitare la replica per la macchina virtuale NomeVM). | - L'ID sottoscrizione potrebbe non essere abilitato per la creazione di VM nel percorso dell'area di destinazione.</br></br>- L'ID sottoscrizione potrebbe non essere abilitato o non avere una quota sufficiente per la creazione di VM di dimensioni specifiche nel percorso dell'area di destinazione.</br></br>- Non è stata trovata una VM di destinazione adatta con un numero di schede di interfaccia di rete (2) corrispondente alla VM di origine per l'ID sottoscrizione nel percorso dell'area di destinazione.| Contattare il [servizio di supporto per la fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per abilitare la creazione di VM delle dimensioni richieste nel percorso di destinazione per la sottoscrizione. Dopo l'abilitazione, provare a eseguire di nuovo l'operazione che ha causato l'errore.

### <a name="fix-the-problem"></a>Risolvere il problema
È possibile contattare il [servizio di supporto per la fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per abilitare la sottoscrizione per la creazione di VM delle dimensioni richieste nel percorso di destinazione.

Se il percorso di destinazione ha un vincolo di capacità, disabilitare la replica e abilitarla in un percorso diverso dove la sottoscrizione ha una quota sufficiente per la creazione di VM delle dimensioni richieste.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificati radice attendibili (codice errore 151066)

Se nella VM non sono presenti tutti i certificati radice trusted più recenti, il processo di abilitazione della replica potrebbe non riuscire. Senza i certificati, le chiamate di autenticazione e autorizzazione del servizio Site Recovery dalla VM non riescono. Viene visualizzato il messaggio di errore del processo di abilitazione della replica di Site Recovery:

**Codice errore** | **Causa possibile** | **Indicazioni**
--- | --- | ---
151066<br></br>**Messaggio**: la configurazione di Site Recovery non è riuscita (151197). | I certificati radice trusted necessari usati per l'autorizzazione e l'autenticazione non sono presenti nel computer. | - Per una VM che esegue il sistema operativo Windows, assicurarsi che i certificati radice trusted siano presenti nel computer. Per informazioni, vedere [Configurare radici attendibili e certificati non consentiti](https://technet.microsoft.com/library/dn265983.aspx).<br></br>- Per una VM che esegue il sistema operativo Linux, seguire le indicazioni per i certificati radice trusted pubblicate dal distributore della versione del sistema operativo Linux.

### <a name="fix-the-problem"></a>Risolvere il problema
**Windows**

Installare tutti gli aggiornamenti di Windows più recenti nella VM in modo che tutti i certificati radice trusted siano presenti nel computer. Se si lavora in un ambiente non connesso, seguire il processo di aggiornamento di Windows standard dell'organizzazione per ottenere i certificati. Se i certificati richiesti non sono presenti nella VM, le chiamate al servizio Site Recovery non riescono per motivi di sicurezza.

Seguire il processo tipico di gestione degli aggiornamenti di Windows o dei certificati in uso nell'organizzazione per ottenere tutti i certificati radice più recenti e l'elenco di revoche di certificati aggiornato nelle VM.

Per verificare che il problema sia stato risolto, passare a login.microsoftonline.com da un browser nella VM.

**Linux**

Seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati aggiornato nella VM.

Poiché SuSE Linux usa i collegamenti simbolici per gestire un elenco di certificati, eseguire questa procedura:

1.  Accedere come utente ROOT.

2.  Eseguire questo comando per cambiare la directory.

      ``# cd /etc/ssl/certs``

1. Controllare se è presente il certificato CA della radice di Symantec.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Se il certificato CA della radice di Symantec non è reperibile, eseguire il comando seguente per scaricare il file. Verificare la presenza di eventuali errori e seguire le azioni consigliate per gli errori di rete.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Controllare se è presente il certificato CA della radice Baltimore.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Se il certificato CA della radice Baltimore non è reperibile, scaricarlo.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Verificare che il certificato DigiCert_Global_Root_CA sia presente.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Se il certificato DigiCert_Global_Root_CA non è reperibile, eseguire i comandi seguenti per scaricarlo.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Eseguire lo script rehash per aggiornare i codici hash dell'oggetto per i certificati appena scaricati.

    ``# c_rehash``

8.  Controllare che gli hash dell'oggetto come collegamenti simbolici vengano creati per i certificati.

    - Comando

      ``# ls -l | grep Baltimore``

    - Output

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Comando

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Comando

      ``# ls -l | grep DigiCert_Global_Root``

    - Output

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Creare una copia del file VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem con nome file b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Creare una copia del file Baltimore_CyberTrust_Root.pem con nome file 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Creare una copia del file DigiCert_Global_Root_CA.pem con nome file 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Controllare che i file siano presenti.  

    - Comando

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195) </br>
- **Causa possibile** </br>
  - Non è possibile connettersi agli endpoint di Site Recovery a causa dell'errore di risoluzione DNS.
  - Questo problema si verifica più spesso durante la riprotezione dopo il failover della macchina virtuale, quando il server DNS non è raggiungibile dall'area di ripristino di emergenza.

- **Risoluzione**
   - Se si usa un DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza. Per verificare se si usa un DNS personalizzato, passare alla macchina virtuale > Rete di ripristino di emergenza > Server DNS. Provare ad accedere al server DNS dalla macchina virtuale. Se non è accessibile, renderla accessibile eseguendo il failover del server DNS o creando la linea del sito tra la rete di ripristino di emergenza e il DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)
- **Causa possibile** </br>
  - Non è possibile connettersi agli endpoint IP4 di identità e autenticazione di Office 365.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli intervalli IP di Office 365 per l'autenticazione.
    Se si usano regole del gruppo di sicurezza di rete di Azure o un proxy firewall per controllare la connettività di rete in uscita della macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli IP di Office 365. Creare una regola NSG basata su [tag del servizio Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) per consentire l'accesso a tutti gli indirizzi IP corrispondenti ad AAD
      - Se in futuro vengono aggiunti nuovi indirizzi ad Azure Active Directory (AAD), è necessario creare nuove regole NSG.

> [!NOTE]
> Se le macchine virtuali si trovano dietro **Standard** bilanciamento del carico interno, allora non avrà accesso a ad esempio indirizzi IP di Office 365 Login.microsoftonline.com per impostazione predefinita. Per modificare **base** interno il tipo di servizio di bilanciamento carico oppure creare indietro all'accesso, come indicato nella [articolo](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)
- **Causa possibile** </br>
  - Non è possibile stabilire la connessione agli endpoint di servizio di Azure Site Recovery.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli [intervalli IP di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a seconda dell'area. Assicurarsi che gli intervalli IP necessari siano accessibili dalla macchina virtuale.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: La replica A2A non riuscita durante il traffico di rete viene indirizzato attraverso il server proxy in locale (151072)
- **Causa possibile** </br>
  - Le impostazioni proxy personalizzate non sono validi e l'agente il servizio Mobility di Azure Site Recovery non rilevare automaticamente le impostazioni proxy di Internet Explorer


- **Risoluzione**
  1. L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e da /etc/environment in Linux.
  2. Se si preferisce impostare proxy solo per il servizio Mobility di Azure Site Recovery, è possibile fornire i dettagli del proxy in ProxyInfo.conf che si trova in:</br>
     - ``/usr/local/InMage/config/`` su ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` su ***Windows***
  3. Il file ProxyInfo.conf deve includere le impostazioni proxy nel formato INI seguente.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Agente di Azure il servizio Mobility di Site Recovery supporta unicamente ***proxy non autenticato***.


### <a name="fix-the-problem"></a>Risolvere il problema
Per consentire [agli URL necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o nella [intervalli IP necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), seguire i passaggi nel [documento linee guida alla rete](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco non trovato nel computer (codice errore 150039)

È necessario inizializzare un nuovo disco collegato alla VM.

**Codice errore** | **Possibili cause** | **Indicazioni**
--- | --- | ---
150039<br></br>**Messaggio**: Il disco dati di Azure (NomeDisco) (%DiskUri) con numero di unità logica (LUN) (ValoreLUN) non è stato mappato a un disco corrispondente presente all'interno della macchina virtuale con lo stesso valore LUN. | - Alla VM è stato collegato un nuovo disco dati che però non è stato inizializzato.</br></br>- Il disco dati all'interno della VM non segnala il valore LUN corretto con il quale il disco è stato collegato alla VM.| Assicurarsi che i dischi dati siano inizializzati e quindi provare a eseguire di nuovo l'operazione.</br></br>Per Windows: [Connettere e inizializzare un nuovo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Per Linux: [Inizializzare un nuovo disco dati in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Risolvere il problema
Assicurarsi che i dischi dati siano stati inizializzati e quindi provare a eseguire di nuovo l'operazione:

- Per Windows: [Connettere e inizializzare un nuovo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Per Linux: [aggiungere un nuovo disco dati in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se il problema persiste, contattare il supporto tecnico.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Uno o più dischi sono disponibili per la protezione (codice di errore 153039)
- **Causa possibile** </br>
  - Se uno o più dischi aggiunti di recente alla macchina virtuale dopo la protezione. 
  - Se uno o più dischi sono stati inizializzati in un secondo momento dopo la protezione della macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema
È possibile scegliere di proteggere i dischi o ignorare l'avviso per rendere nuovamente integro lo stato di replica della macchina virtuale.</br>
1. Per proteggere i dischi. Passare a elementi replicati > macchina virtuale > dischi > fare clic sul disco non protetto > Abilitazione replica.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Per ignorare l'avviso. Passare a elementi replicati > macchina virtuale > fare clic su Ignora avviso nella sezione Panoramica.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Rimuovere la macchina virtuale dall'insieme di credenziali completata con informazioni (codice di errore 150225)
Al momento della protezione della macchina virtuale, Azure Site Recovery crea alcuni collegamenti nella macchina virtuale di origine. Quando si rimuove la protezione o disabilitare la replica, Azure Site Recovery rimuovere i collegamenti seguenti come parte del processo di pulizia. Nel caso in cui la macchina virtuale dispone di un blocco di risorsa il processo ottiene completato con le informazioni. Indica che la macchina virtuale è stato rimosso dall'insieme di credenziali di servizi di ripristino, ma alcuni dei collegamenti non aggiornati non è stato eseguita la pulizia del computer di origine.

È possibile ignorare questo avviso se non si intende proteggere questa macchina virtuale nuovamente in futuro. Tuttavia, se è necessario proteggere questa macchina virtuale in un secondo momento quindi è necessario pulire i collegamenti come indicato nei passaggi seguenti. 

**In caso contrario la pulitura quindi:**

1.  Durante la fase di abilitazione della replica tramite insieme di credenziali di Recovery services, macchina virtuale non sarà elencata. 
2.  Se si tenta di proteggere la macchina virtuale tramite **macchina virtuale > Impostazioni > ripristino di emergenza** si verifica non è riuscita con errore "*non è possibile abilitare la replica a causa di collegamenti esistenti di risorse non aggiornati nella macchina virtuale*".


### <a name="fix-the-problem"></a>Risolvere il problema

>[!NOTE]
>
>Azure Site Recovery non elimina macchina virtuale di origine o impattano in alcun modo durante l'esecuzione di passaggi riportati di seguito.
>

1. Rimuovere il blocco dalla macchina virtuale o macchina virtuale di gruppo di risorse. Ad esempio:  Di sotto di macchina virtuale di nome "MoveDemo" ha il blocco di risorsa che deve essere eliminata.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Scarica script [Rimuovi configurazione di Azure Site Recovery non aggiornata](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Eseguire lo script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Specificare il nome della sottoscrizione ID, macchina virtuale di gruppo di risorse e la macchina virtuale come parametro.
5. Se viene richiesto di credenziali di Azure, specificare che e verificare che lo script ottiene eseguito senza errori. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Non è possibile abilitare la replica a causa di collegamenti esistenti di risorse non aggiornati nella macchina virtuale (codice di errore 150226)

**Causa: Macchina virtuale ha una configurazione non aggiornata dalla protezione di Site Recovery precedente a sinistra**

La configurazione non aggiornata potrebbe rimanere in una VM di Azure nei casi seguenti:

- È abilitata la replica per la macchina virtuale di Azure usando Site Recovery e quindi disabilitare la replica, ma il **macchina virtuale di origine era un blocco di risorsa**.
- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.
- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato il gruppo di risorse contenente l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.

### <a name="fix-the-problem"></a>Risolvere il problema

>[!NOTE]
>
>Azure Site Recovery non elimina macchina virtuale di origine o impattano in alcun modo durante l'esecuzione di passaggi riportati di seguito.


1. Rimuovere il blocco dalla macchina virtuale o macchina virtuale di gruppo di risorse, se presenti. *Ad esempio:* Di sotto di macchina virtuale di nome "MoveDemo" ha il blocco di risorsa che deve essere eliminata.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Scarica script [Rimuovi configurazione di Azure Site Recovery non aggiornata](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Eseguire lo script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Specificare il nome della sottoscrizione ID, macchina virtuale di gruppo di risorse e la macchina virtuale come parametro.
5. Se viene richiesto di credenziali di Azure, specificare che e verificare che lo script ottiene eseguito senza errori.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Non è possibile visualizzare il macchina virtuale di Azure o gruppo di risorse per la selezione di "Abilitazione della replica"

 **Causa 1:  Il gruppo di risorse e la macchina virtuale di origine si trovano in una posizione diversa**
 
Azure Site Recovery attualmente mandati che il gruppo di risorse di area e le macchine virtuali di origine devono essere nella stessa posizione. Se non è questo il caso potrebbe non essere in grado di trovare la macchina virtuale o un gruppo di risorse durante il periodo di protezione. 

**Come soluzione alternativa**, è possibile abilitare la replica dalla macchina virtuale anziché l'insieme di credenziali di servizi di ripristino. Passare alla macchina virtuale di origine > Proprietà > Abilita la replica e ripristino di emergenza.

**Causa 2: Il gruppo di risorse non appartiene alla sottoscrizione selezionata**

Potrebbe non essere possibile trovare il gruppo di risorse durante il periodo di protezione se il gruppo non fa parte della sottoscrizione specificata. Assicurarsi che il gruppo di risorse appartenga alla sottoscrizione in uso.

 **Causa 3: Configurazione non aggiornata**
 
Se la macchina virtuale che si desidera abilitare per la replica non viene visualizzata, potrebbe essere a causa di una configurazione di Site Recovery non aggiornata nella macchina virtuale di Azure. La configurazione non aggiornata potrebbe rimanere in una VM di Azure nei casi seguenti:

- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.
- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato il gruppo di risorse contenente l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.

- È abilitata la replica per la macchina virtuale di Azure usando Site Recovery e quindi disabilitare la replica, ma la VM di origine era un blocco di risorsa.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
>
> Assicurarsi di aggiornare il modulo ""AzureRM.Resources"" prima di usare lo script seguente. Azure Site Recovery non elimina macchina virtuale di origine o impattano in alcun modo durante l'esecuzione di passaggi riportati di seguito.
>

1. Rimuovere il blocco dalla macchina virtuale o macchina virtuale di gruppo di risorse, se presenti. *Ad esempio:* Di sotto di macchina virtuale di nome "MoveDemo" ha il blocco di risorsa che deve essere eliminata.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Scarica script [Rimuovi configurazione non aggiornata](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Eseguire lo script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Specificare il nome della sottoscrizione ID, macchina virtuale di gruppo di risorse e la macchina virtuale come parametro.
5. Se viene richiesto di credenziali di Azure, specificare che e verificare che lo script ottiene eseguito senza errori.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Impossibile selezionare la macchina virtuale per eseguire la procedura di protezione
 **Causa 1:  Un'estensione installata nella macchina virtuale si trova in stato di errore o non risponde** <br>
 Passare a Macchine virtuali > Impostazioni > Estensioni e verificare se esistono eventuali estensioni che si trovano in uno stato di errore. Disinstallare l'estensione in stato di errore e ripetere la procedura di protezione della macchina virtuale.<br>
 **Causa 2:  [Lo stato di provisioning della VM non è valido](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Lo stato di provisioning della VM non è valido (codice errore 150019)

Per abilitare la replica sulla VM, lo stato di provisioning deve essere **Riuscito**. Per verificare lo stato della macchina virtuale, seguire questa procedura.

1.  Selezionare **Esplora risorse** da **Tutti i servizi** nel portale di Azure.
2.  Espandere l'elenco **Sottoscrizioni** e selezionare la sottoscrizione.
3.  Espandere l'elenco **Gruppi di risorse** e selezionare il gruppo di risorse della VM.
4.  Espandere l'elenco **Risorse** e selezionare la macchina virtuale
5.  Controllare il campo **provisioningState** nella visualizzazione Istanza a destra.

### <a name="fix-the-problem"></a>Risolvere il problema

- Se **provisioningState** è **Non riuscito**, contattare il supporto tecnico specificando i dettagli necessari per risolvere il problema.
- Se **provisioningState** è **Aggiornamento**, potrebbe essere in corso la distribuzione di un'altra estensione. Controllare se sono in corso operazioni nella VM, attenderne il completamento e provare a eseguire di nuovo il processo **Abilita replica** di Site Recovery non riuscito.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Non è possibile selezionare la rete virtuale di destinazione: la scheda di selezione della rete è inattiva.

**Causa 1: La macchina virtuale è collegata a una rete che è già mappata a una "rete di destinazione".**
- Se la macchina virtuale di origine fa parte di una rete virtuale e un'altra macchina virtuale della stessa rete virtuale è già mappata a una rete nel gruppo di risorse di destinazione, l'elenco a discesa di selezione della rete è disabilitato per impostazione predefinita.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Causa 2: in precedenza la macchina virtuale è stata protetta tramite Azure Site Recovery disabilitando la replica.**
 - La disabilitazione della replica di una macchina virtuale non elimina il mapping di rete. Questo deve essere eliminato dall'insieme di credenziali di Servizi di ripristino in cui è stata protetta la macchina virtuale. </br>
 Passare a Insieme di credenziali di Servizi di ripristino > Infrastruttura di Site Recovery > Mapping di rete. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - La rete di destinazione impostata durante la configurazione del ripristino di emergenza può essere modificata dopo la configurazione iniziale, in seguito alla protezione della macchina virtuale. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - La modifica del mapping di rete interessa tutte le macchine virtuali protette che usano il mapping specifico.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>Errore del servizio Copia Shadow del volume/COM+ (codice di errore 151025)

**Codice errore** | **Possibili cause** | **Indicazioni**
--- | --- | ---
151025<br></br>**Messaggio**: Impossibile installare l'estensione Site Recovery | - Il servizio "Applicazione di sistema COM+" è disabilitato.</br></br>- Il servizio "Copia Shadow del volume" è disabilitato.| Impostare i servizi "Applicazione di sistema COM+" e "Copia Shadow del volume" sulla modalità di avvio automatica o manuale.

### <a name="fix-the-problem"></a>Risolvere il problema

È possibile aprire la console "Servizi" e verificare che i servizi "Applicazione di sistema COM+" e "Copia Shadow del volume" non siano impostati su "Disabilitato" per "Tipo di avvio".
  ![Errore COM](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Dimensioni del disco gestito non supportate (codice di errore 150172)


**Codice errore** | **Possibili cause** | **Indicazioni**
--- | --- | ---
150172<br></br>**Messaggio**: Protection couldn't be enabled for the virtual machine as it has (DiskName) with size (DiskSize) that is lesser than the minimum supported size 1024 MB (Impossibile abilitare la protezione per la macchina virtuale perché contiene (DiskName) con dimensioni (DiskSize), valore inferiore alla dimensione minima supportata, ovvero 1024 MB). | - Le dimensioni del disco sono inferiori alle dimensioni supportate (1024 MB)| Assicurarsi che le dimensioni del disco siano comprese nell’intervallo di dimensioni supportate, quindi ripetere l'operazione.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Abilitare la protezione non riuscita come nome del dispositivo indicato nella configurazione GRUB anziché UUID (codice di errore 151126)

**Causa possibile:** </br>
I file di configurazione di GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" o"/etc/default/grub") possono contenere il valore per i parametri **root** (radice) e **resume** (riprendi) come nomi effettivi dei dispositivi anziché l'UUID. Site Recovery impone l'approccio UUID in quanto il nome dei dispositivi è soggetto a cambiamento al riavvio della macchina virtuale; la stessa potrebbe non essere visualizzata con lo stesso nome in caso di failover, con conseguenti problemi. Ad esempio: </br>


- La riga seguente si trova nel file GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- La riga seguente si trova nel file GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Se si osserva la stringa precedente formattata in grassetto, si noterà che GRUB contiene i nomi dei dispositivi effettivi per i parametri "root"(radice) e "resume" (riprendi) anziché gli UUID.

**Come correggere:**<br>
I nomi dei dispositivi devono essere sostituiti con l'UUID corrispondente.<br>


1. Individuare l'UUID del dispositivo eseguendo il comando "blkid \<nome dispositivo >". Ad esempio:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site Recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  Site Recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
