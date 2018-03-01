---
title: Risoluzione dei problemi di Azure Site Recovery per gli errori di replica da Azure ad Azure | Microsoft Docs
description: Risoluzione dei problemi e degli errori che si verificano quando si esegue la replica di macchine virtuali di Azure per il ripristino di emergenza
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: sujayt
ms.openlocfilehash: 7292948c40b184a58eb3e27aecac28e2227a29f8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Risolvere i problemi di replica delle VM da Azure ad Azure

Questo articolo descrive i problemi comuni in Azure Site Recovery quando si eseguono la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra e illustra come risolverli. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemi di quota delle risorse di Azure (codice errore 150097)
È necessario che la sottoscrizione sia abilitata per la creazione di VM di Azure nell'area di destinazione che si prevede di usare come area per il ripristino di emergenza. Per la sottoscrizione deve inoltre essere abilitata una quota sufficiente per la creazione di VM di dimensioni specifiche. Per impostazione predefinita, Site Recovery usa, per la VM di destinazione, le stesse dimensioni della VM di origine. Se le dimensioni corrispondenti non sono disponibili, vengono selezionate automaticamente le dimensioni più vicine possibili. Se non ci sono dimensioni corrispondenti che supportano la configurazione della VM di origine, viene visualizzato il messaggio di errore seguente:

**Codice errore** | **Possibili cause** | **Consiglio**
--- | --- | ---
150097<br></br>**Messaggio**: Replication couldn't be enabled for the virtual machine VmName (Non è stato possibile abilitare la replica per la macchina virtuale NomeVM). | - L'ID sottoscrizione potrebbe non essere abilitato per la creazione di VM nel percorso dell'area di destinazione.</br></br>- L'ID sottoscrizione potrebbe non essere abilitato o non avere una quota sufficiente per la creazione di VM di dimensioni specifiche nel percorso dell'area di destinazione.</br></br>- Non è stata trovata una VM di destinazione adatta con un numero di schede di interfaccia di rete (2) corrispondente alla VM di origine per l'ID sottoscrizione nel percorso dell'area di destinazione.| Contattare il [servizio di supporto per la fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per abilitare la creazione di VM delle dimensioni richieste nel percorso di destinazione per la sottoscrizione. Dopo l'abilitazione, provare a eseguire di nuovo l'operazione che ha causato l'errore.

### <a name="fix-the-problem"></a>Risolvere il problema
È possibile contattare il [servizio di supporto per la fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per abilitare la sottoscrizione per la creazione di VM delle dimensioni richieste nel percorso di destinazione.

Se il percorso di destinazione ha un vincolo di capacità, disabilitare la replica e abilitarla in un percorso diverso dove la sottoscrizione ha una quota sufficiente per la creazione di VM delle dimensioni richieste.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificati radice trusted (codice errore 151066)

Se nella VM non sono presenti tutti i certificati radice trusted più recenti, il processo di abilitazione della replica potrebbe non riuscire. Senza i certificati, le chiamate di autenticazione e autorizzazione del servizio Site Recovery dalla VM non riescono. Viene visualizzato il messaggio di errore del processo di abilitazione della replica di Site Recovery:

**Codice errore** | **Causa possibile** | **Raccomandazioni**
--- | --- | ---
151066<br></br>**Messaggio**: La configurazione di Site Recovery non è riuscita. | I certificati radice trusted necessari usati per l'autorizzazione e l'autenticazione non sono presenti nel computer. | - Per una VM che esegue il sistema operativo Windows, assicurarsi che i certificati radice trusted siano presenti nel computer. Per informazioni, vedere [Configurare radici attendibili e certificati non consentiti](https://technet.microsoft.com/library/dn265983.aspx).<br></br>- Per una VM che esegue il sistema operativo Linux, seguire le indicazioni per i certificati radice trusted pubblicate dal distributore della versione del sistema operativo Linux.

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

3. Controllare se è presente il certificato CA della radice di Symantec.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Se il certificato CA della radice di Symantec non è reperibile, eseguire il comando seguente per scaricare il file. Verificare la presenza di eventuali errori e seguire le azioni consigliate per gli errori di rete.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Controllare se è presente il certificato CA della radice Baltimore.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Se il certificato CA della radice Baltimore non è reperibile, scaricarlo.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Verificare che il certificato DigiCert_Global_Root_CA sia presente.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Se il certificato DigiCert_Global_Root_CA non è reperibile, eseguire i comandi seguenti per scaricarlo.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Eseguire lo script rehash per aggiornare i codici hash dell'oggetto per i certificati appena scaricati.

    ``# c_rehash``

10. Controllare che gli hash dell'oggetto come collegamenti simbolici vengano creati per i certificati.

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

11. Creare una copia del file VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem con nome file b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Creare una copia del file Baltimore_CyberTrust_Root.pem con nome file 653b494a.0

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

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la VM è protetta da un firewall o usa regole dei gruppi di sicurezza di rete (NSG) per controllare la connettività in uscita, potrebbe venire visualizzato uno di questi messaggi di errore:

**Codici di errore** | **Possibili cause** | **Raccomandazioni**
--- | --- | ---
151037<br></br>**Messaggio**: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery. | - Si sta usando un gruppo di sicurezza di rete (NSG) per controllare l'accesso in uscita nella VM e gli intervalli IP richiesti non sono inclusi nell'elenco elementi consentiti per l'accesso in uscita.</br></br>- Si stanno usando strumenti firewall di terze parti e gli intervalli IP/URL richiesti non sono inclusi nell'elenco elementi consentiti.</br>| - Se si sta usando un proxy firewall per controllare la connettività di rete in uscita nella VM, verificare che gli intervalli IP dei data center o gli URL prerequisiti siano inclusi nell'elenco elementi consentiti. Per informazioni, vedere le [indicazioni per i proxy firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>- Se si stanno usando regole dei gruppi di sicurezza di rete per controllare la connettività di rete in uscita nella VM, verificare che gli intervalli IP dei data center prerequisiti siano inclusi nell'elenco elementi consentiti. Per altre informazioni, vedere le [indicazioni per i gruppi di sicurezza di rete](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Messaggio**: La configurazione di Site Recovery non è riuscita. | Non è possibile stabilire la connessione agli endpoint di servizio di Site Recovery. | - Se si sta usando un proxy firewall per controllare la connettività di rete in uscita nella VM, verificare che gli intervalli IP dei data center o gli URL prerequisiti siano inclusi nell'elenco elementi consentiti. Per informazioni, vedere le [indicazioni per i proxy firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>- Se si stanno usando regole dei gruppi di sicurezza di rete per controllare la connettività di rete in uscita nella VM, verificare che gli intervalli IP dei data center prerequisiti siano inclusi nell'elenco elementi consentiti. Per altre informazioni, vedere le [indicazioni per i gruppi di sicurezza di rete](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Risolvere il problema
Per includere nell'elenco elementi consentiti gli [URL necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), seguire i passaggi nel [documento di indicazioni sulle reti](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco non trovato nel computer (codice errore 150039)

È necessario inizializzare un nuovo disco collegato alla VM.

**Codice errore** | **Possibili cause** | **Raccomandazioni**
--- | --- | ---
150039<br></br>**Messaggio**: Il disco dati di Azure (NomeDisco) (%DiskUri) con numero di unità logica (LUN) (ValoreLUN) non è stato mappato a un disco corrispondente presente all'interno della macchina virtuale con lo stesso valore LUN. | - Alla VM è stato collegato un nuovo disco dati che però non è stato inizializzato.</br></br>- Il disco dati all'interno della VM non segnala il valore LUN corretto con il quale il disco è stato collegato alla VM.| Assicurarsi che i dischi dati siano inizializzati e quindi provare a eseguire di nuovo l'operazione.</br></br>Per Windows: [Connettere e inizializzare un nuovo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Per Linux: [Inizializzare un nuovo disco dati in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Risolvere il problema
Assicurarsi che i dischi dati siano stati inizializzati e quindi provare a eseguire di nuovo l'operazione:

- Per Windows: [Connettere e inizializzare un nuovo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Per Linux: [aggiungere un nuovo disco dati in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se il problema persiste, contattare il supporto tecnico.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Impossibile vedere la VM di Azure tra le opzioni per l'abilitazione della replica

Se la macchina virtuale che si desidera abilitare per la replica non viene visualizzata, potrebbe essere a causa di una configurazione di Site Recovery non aggiornata nella macchina virtuale di Azure. La configurazione non aggiornata potrebbe rimanere in una VM di Azure nei casi seguenti:

- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.
- È stata abilitata la replica per la VM di Azure usando Site Recovery e quindi è stato eliminato il gruppo di risorse contenente l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella VM.

### <a name="fix-the-problem"></a>Risolvere il problema

È possibile usare lo [script per la rimozione della configurazione non aggiornata di Azure Site Recovery](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) e rimuovere la configurazione dalla VM di Azure. Dopo la rimozione della configurazione non aggiornata dovrebbe essere possibile visualizzare la macchina virtuale.

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

## <a name="next-steps"></a>Passaggi successivi
[Replicare le macchine virtuali di Azure](site-recovery-replicate-azure-to-azure.md)
