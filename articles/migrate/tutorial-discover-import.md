---
title: Valutare i server locali usando un file CSV importato con Valutazione server di Azure Migrate
description: Informazioni su come individuare server locali per la migrazione ad Azure usando un file CSV importato in Valutazione server di Azure Migrate
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 743f18ce72e3f14fe54e0bbadff254ea03fc6278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604224"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Esercitazione: Valutare i server con un file CSV importato

Il percorso di migrazione ad Azure prevede l'individuazione dei carichi di lavoro e dell'inventario locale. 

Questa esercitazione mostra come valutare computer locali con lo strumento Azure Migrate: Valutazione server, usando un file CSV importato. 

Se si usa un file CSV, non è necessario configurare l'appliance di Azure Migrate per individuare e valutare i server. È possibile controllare i dati condivisi nel file e buona parte dei dati è facoltativa. Questo metodo è utile se:

- Si vuole creare una rapida valutazione iniziale prima di distribuire l'appliance.
- Non è possibile distribuire l'appliance di Azure Migrate nell'organizzazione.
- Non è possibile condividere le credenziali che consentono l'accesso ai server locali.
- I vincoli di sicurezza impediscono la raccolta e l'invio di dati raccolti dall'appliance ad Azure.

> [!NOTE]
> Non è possibile eseguire la migrazione di server importati usando un file CSV.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Configurare un account Azure
> * Configurare un progetto di Azure Migrate
> * Preparare un file CSV
> * Importare il file
> * Valutazione server

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È possibile aggiungere fino a 20.000 server in un unico file CSV e in un progetto di Azure Migrate. 
- I nomi del sistema operativo specificati nel CSV devono corrispondere o contenere i [nomi supportati](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto di Azure Migrate, è necessario un account con:
- Autorizzazioni di collaboratore o proprietario per una sottoscrizione di Azure.
- Autorizzazioni per la registrazione di app Azure Active Directory.

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure](./media/tutorial-discover-import/search-subscription.png)

2. Nella pagina **Sottoscrizioni** selezionare la sottoscrizione in cui creare un progetto di Azure Migrate. 
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per verificare l'accesso e assegnare un ruolo](./media/tutorial-discover-import/azure-account-access.png)

6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account](./media/tutorial-discover-import/assign-role.png)

7. Nel portale cercare gli utenti e in **Servizi** selezionare **Utenti**.
8. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    ![Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto di Azure Migrate se non esiste già.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-discover-import/new-project.png)

7. Selezionare **Crea**.
8. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate.

Lo strumento **Azure Migrate: Valutazione server** viene aggiunto per impostazione predefinita al nuovo progetto.

![Pagina che mostra lo strumento Valutazione server aggiunto per impostazione predefinita](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Preparare il CSV

Scaricare il modello CSV e aggiungervi le informazioni sul server.

### <a name="download-the-template"></a>Scaricare il modello

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali**selezionare **Importa tramite file CSV**.
3. Selezionare **Scarica** per scaricare il modello CSV. In alternativa, è possibile [scaricarlo direttamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Scaricare il modello CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Aggiungere informazioni sul server

Raccogliere i dati sul server e aggiungerli al file CSV.

- Per raccogliere dati, è possibile esportarli dagli strumenti usati per la gestione di server locali, ad esempio VMware vSphere o il database di gestione della configurazione (CMDB).
- Per esaminare i dati di esempio, scaricare il [file di esempio](https://go.microsoft.com/fwlink/?linkid=2108405).

Nella tabella seguente vengono riepilogati i campi del file da compilare:

**Nome campo** | **Obbligatorio** | **Dettagli**
--- | --- | ---
**Nome server** | Sì | È consigliabile specificare il nome di dominio completo (FQDN).
**Indirizzo IP** | No | Indirizzo del server.
**Core** | Sì | Numero di core del processore allocati al server.
**Memoria** | Sì | RAM totale, in MB, allocata al server.
**Nome sistema operativo** | Sì | Sistema operativo server. <br/> I nomi dei sistemi operativi che corrispondono o che contengono i nomi in [questo](#supported-operating-system-names) elenco sono riconosciuti dalla valutazione.
**Versione del sistema operativo** | No | Versione del sistema operativo server.
**Architettura del sistema operativo** | No | Architettura del sistema operativo server <br/> I valori validi sono: x64, x86, amd64, 32 bit o 64 bit
**Numero di dischi** | No | Non necessario se sono presenti i dettagli dei singoli dischi.
**Dimensioni disco 1**  | No | Dimensioni massime del disco, in GB.<br/>È possibile aggiungere dettagli per altri dischi tramite[aggiunta di colonne](#add-multiple-disks) nel modello. È possibile aggiungere fino a otto versioni del disco.
**Operazioni di lettura disco 1** | No | Operazioni di lettura da disco al secondo.
**Operazioni di scrittura disco 1** | No | Operazioni di scrittura su disco al secondo.
**Velocità effettiva lettura da disco 1** | No | Dati letti dal disco al secondo, in MB al secondo.
**Velocità effettiva scrittura da disco 1** | No | Dati scritti sul disco al secondo, in MB al secondo.
**Percentuale utilizzo CPU** | No | Percentuale di CPU usata.
**Percentuale utilizzo memoria** | No | Percentuale di RAM usata.
**Totale operazioni di lettura dischi** | No | Operazioni di lettura da disco al secondo.
**Totale operazioni di scrittura dischi** | No | Operazioni di scrittura su disco al secondo.
**Totale velocità effettiva lettura da dischi** | No | Dati letti dal disco, in MB al secondo.
**Totale velocità effettiva scrittura da dischi** | No | Dati scritti sul disco, in MB al secondo.
**Velocità effettiva della rete in ingresso** | No | Dati ricevuti dal server, in MB al secondo.
**Velocità effettiva della rete in uscita** | No | Dati trasmessi dal server, in MB al secondo.
**Tipo di firmware** | No | Firmware del server. I valori possono essere "BIOS" o "UEFI".
**Indirizzo MAC**| No | Indirizzo MAC server.


### <a name="add-operating-systems"></a>Aggiungere sistemi operativi

La valutazione riconosce i nomi specifici del sistema operativo. Qualsiasi nome del sistema operativo specificato deve corrispondere a una delle stringhe incluse nell'elenco di [nomi supportati](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Aggiungere più dischi

Il modello fornisce campi predefiniti per il primo disco. È possibile aggiungere colonne simili per un totale di otto dischi.

Ad esempio, per specificare tutti i campi per un secondo disco, aggiungere queste colonne:

- Dimensioni disco 2
- Operazioni di lettura disco 2
- Operazioni di scrittura disco 2
- Velocità effettiva lettura disco 2
- Velocità effettiva scrittura disco 2


## <a name="import-the-server-information"></a>Importare le informazioni sul server

Dopo aver aggiunto le informazioni nel modello CSV, importare il file CSV in Valutazione server.

1. In Azure Migrate, in **Individua macchine virtuali**, passare al modello completato.
2. Selezionare **Importa**.
3. Viene visualizzato lo stato dell'importazione.
    - Se nello stato vengono visualizzati avvisi, è possibile correggerli o continuare senza risolverli.
    - Per una maggiore accuratezza della valutazione, migliorare le informazioni sul server come suggerito negli avvisi.
    - Per visualizzare e correggere gli avvisi, selezionare **Download warning details .CSV** (Scarica i dettagli degli avvisi in formato CSV). Questa operazione scarica il file CSV con gli avvisi inclusi. Esaminare gli avvisi e correggere i problemi se necessario.
    - Se vengono visualizzati errori per cui lo stato dell'importazione è **Non riuscito**, è necessario correggerli prima di continuare:
        1. Scaricare il file CSV, che ora include i dettagli degli errori.
        1. Esaminare e risolvere gli errori se necessario. 
        1. Caricare di nuovo il file modificato.
4. Quando lo stato dell'importazione è **Completato**, vengono importate le informazioni sul server. Aggiornare se il processo di importazione sembra essere incompleto.

## <a name="update-server-information"></a>Aggiornare le informazioni sul server

È possibile aggiornare le informazioni su un server importando nuovamente i dati per il server con lo stesso **Nome del server**. Non è possibile modificare il campo **Nome del server**. L'eliminazione di server è attualmente supportata.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Per verificare se i server vengono visualizzati nel portale di Azure dopo l'individuazione:

1. Aprire il dashboard di Azure Migrate.
2. Nella pagina **Azure Migrate - Server** > **Azure Migrate: Valutazione server** selezionare l'icona che mostra il numero di **Server individuati**.
3. Selezionare la scheda **Basati sull'importazione**.



## <a name="supported-operating-system-names"></a>Nomi dei sistemi operativi supportati

I nomi del sistema operativo specificati nel file CSV devono corrispondere o contenere i nomi di questo elenco. In caso contrario, non sarà possibile valutarli. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * È stato creato un progetto di Azure Migrate 
> * Sono stati individuati i server usando un file CSV importato. A questo punto, eseguire una valutazione per la [migrazione di macchine virtuali VMware a macchine virtuali di Azure](tutorial-assess-vmware.md).
