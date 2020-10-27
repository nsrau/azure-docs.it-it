---
title: Prezzi di Backup di Azure
description: Informazioni su come stimare i costi per preventivare i prezzi di Backup di Azure.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654532"
---
# <a name="azure-backup-pricing"></a>Prezzi di Backup di Azure

Per informazioni, visitare la [pagina di prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Scaricare le stime dettagliate per i prezzi di Backup di Azure

Per stimare i costi per scopi di budget o confronto, scaricare lo [strumento di stima dei prezzi di Backup di Azure](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Che cosa contiene lo strumento di stima?

Il foglio di stima dei costi di Backup di Azure offre un'opzione che consente di stimare tutti i possibili carichi di lavoro da sottoporre a backup con Backup di Azure. Questi carichi di lavoro includono:

- Macchine virtuali di Azure
- Server locali
- SQL in VM di Azure
- SAP HANA in VM di Azure
- Condivisioni file di Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Stimare i costi per il backup delle VM di Azure o i server locali

Per stimare i costi del backup di VM di Azure o server locali con Backup di Azure, sono necessari i parametri seguenti:

- Dimensioni delle VM o dei server locali di cui eseguire il backup
  - Immettere le "dimensioni usate" dei dischi o dei server di cui eseguire il backup

- Numero di server con tali dimensioni

- Qual è l'entità prevista di varianza dei dati in questi server?<br>
  La varianza si riferisce alla quantità di modifiche nei dati. Se ad esempio una VM contiene 200 GB di dati per il backup di cui 10 GB cambiano ogni giorno, la varianza giornaliera sarà del 5%.

  - Una varianza più alta implica che viene eseguito il backup di più dati

  - Scegliere una varianza **Bassa** o **Moderata** per i file server e **Alta** se si eseguono database

  - Se si conosce la percentuale di varianza, è possibile usare l'opzione **Enter your own%** (Immettere una percentuale personalizzata)

- Scegliere i criteri di backup

  - Per quanto tempo si prevede di conservare i backup giornalieri? (in giorni)

  - Per quanto tempo si prevede di conservare i backup settimanali? (in settimane)

  - Per quanto tempo si prevede di conservare i backup mensili? (in mesi)

  - Per quanto tempo si prevede di conservare i backup annuali? (in anni)

  - Per quanto tempo si prevede di conservare gli snapshot di ripristino istantaneo? (1-5 giorni)

    - Questa opzione consente di eseguire rapidamente il ripristino di dati risalenti fino a sette giorni prima tramite snapshot archiviati su dischi.

- **Facoltativo** - Backup selettivo dei dischi

  - Se si usa l'opzione **Backup selettivo dei dischi** durante il backup delle macchine virtuali di Azure, scegliere l'opzione **Exclude Disk** (Escludi disco) e immettere la percentuale di dischi da escludere dal backup in termini di dimensioni. Se ad esempio una VM è connessa a tre dischi, ognuno dei quali con 200 GB usati, e se ne vogliono escludere due dal backup, immettere 66,7%.

- **Facoltativo** - Ridondanza dell'archivio di backup

  - Indica la ridondanza dell'account di archiviazione in cui andranno i dati di backup. È consigliabile usare l'opzione **Archiviazione con ridondanza geografica** per la massima disponibilità. Con questa opzione una copia dei dati viene mantenuta in un'area diversa ed è quindi possibile soddisfare più standard di conformità. Cambiare la ridondanza in **Archiviazione con ridondanza locale** se il backup riguarda ambienti di sviluppo o di test e non è necessario eseguirlo a livello aziendale. Selezionare l'opzione **Archiviazione con ridondanza geografica e accesso in lettura** nel foglio per sapere quali saranno i costi se per i backup è abilitata l'opzione [Ripristino tra aree](backup-azure-arm-restore-vms.md#cross-region-restore).

- **Facoltativo** - Modificare i prezzi a livello di area o applicare tariffe scontate

  - Per controllare le stime per un'area diversa o per tariffe scontate, selezionare **Sì** per l'opzione **Try estimates for a different region?** (Provare le stime per un'area diversa?) e immettere le tariffe con cui eseguire le stime.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Stimare i costi per il backup di SQL Server in macchine virtuali di Azure

Per stimare i costi del backup di SQL Server in esecuzione in VM di Azure con Backup di Azure, sono necessari i parametri seguenti:

- Dimensioni delle istanze di SQL Server di cui eseguire il backup

- Numero di istanze di SQL Server con le suddette dimensioni

- Qual è la compressione prevista per i dati di backup di SQL Server?

  - La maggior parte dei clienti di Backup di Azure nota che i dati di backup hanno una compressione dell'80% rispetto alle dimensioni di SQL Server quando la compressione di SQL è **abilitata** .

  - Se si prevede che la compressione sia diversa, immettere un numero in questo campo

- Quali sono le dimensioni previste dei backup dei log?

  - La percentuale indica le dimensioni giornaliere dei log come percentuale delle dimensioni di SQL Server

- Qual è l'entità prevista di varianza quotidiana dei dati in questi server?

  - In genere, i database hanno una varianza alta

  - Se si conosce la percentuale di varianza, è possibile usare l'opzione **Enter your own%** (Immettere una percentuale personalizzata)

- Scegliere i criteri di backup

  - Tipo di backup

    - Il criterio più efficace che è possibile scegliere è **Daily differentials** (Differenziali giornalieri) con i backup completi settimanali/mensili/annuali. Backup di Azure consente di eseguire il ripristino dai backup differenziali anche tramite singolo clic.

    - È inoltre possibile scegliere un criterio con backup completi giornalieri/settimanali/mensili/annuali. Questa opzione consumerà una quantità di spazio di archiviazione leggermente superiore a quella della prima opzione.

  - Per quanto tempo si prevede di conservare i backup dei log? (in giorni) [7-35]

  - Per quanto tempo si prevede di conservare i backup giornalieri? (in giorni)

  - Per quanto tempo si prevede di conservare i backup settimanali? (in settimane)

  - Per quanto tempo si prevede di conservare i backup mensili? (in mesi)

  - Per quanto tempo si prevede di conservare i backup annuali? (in anni)

- **Facoltativo** - Ridondanza dell'archivio di backup

  - Indica la ridondanza dell'account di archiviazione in cui andranno i dati di backup. È consigliabile usare l'opzione **Archiviazione con ridondanza geografica** per la massima disponibilità. Con questa opzione una copia dei dati viene mantenuta in un'area diversa ed è quindi possibile soddisfare più standard di conformità. Cambiare la ridondanza in **Archiviazione con ridondanza locale** se il backup riguarda ambienti di sviluppo o di test e non è necessario eseguirlo a livello aziendale.

- **Facoltativo** - Modificare i prezzi a livello di area o applicare tariffe scontate

  - Per controllare le stime per un'area diversa o per tariffe scontate, selezionare **Sì** per l'opzione **Try estimates for a different region?** (Provare le stime per un'area diversa?) e immettere le tariffe con cui eseguire le stime.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Stimare i costi per il backup di server SAP HANA in macchine virtuali di Azure

Per stimare i costi del backup di server SAP HANA in esecuzione in VM di Azure con Backup di Azure, sono necessari i parametri seguenti:

- Dimensioni totali dei database SAP HANA di cui eseguire il backup. Deve corrispondere alla somma delle dimensioni del backup completo di ogni database, come riportato da SAP HANA.
- Numero di server SAP HANA con le suddette dimensioni
- Quali sono le dimensioni previste dei backup dei log?
  
  - La percentuale indica le dimensioni giornaliere medie dei log come percentuale delle dimensioni totali dei database SAP HANA di cui eseguire il backup nel server SAP HANA
- Qual è l'entità prevista di varianza quotidiana dei dati in questi server?
  - La percentuale indica le dimensioni giornaliere medie della varianza come percentuale delle dimensioni totali dei database SAP HANA di cui eseguire il backup nel server SAP HANA
  - In genere, i database hanno una varianza alta
  - Se si conosce la percentuale di varianza, è possibile usare l'opzione **Enter your own%** (Immettere una percentuale personalizzata)
- Scegliere i criteri di backup
  - Tipo di backup
    - Il criterio più efficace che è possibile scegliere è **Daily differentials** (Differenziali giornalieri) con i backup completi **settimanali/mensili/annuali** . Backup di Azure consente di eseguire il ripristino dai backup differenziali anche tramite singolo clic.
    - È inoltre possibile scegliere un criterio con backup completi **giornalieri/settimanali/mensili/annuali** . Questa opzione consumerà una quantità di spazio di archiviazione leggermente superiore a quella della prima opzione.
  - Per quanto tempo si prevede di conservare i backup dei log? (in giorni) [7-35]
  - Per quanto tempo si prevede di conservare i backup giornalieri? (in giorni)
  - Per quanto tempo si prevede di conservare i backup settimanali? (in settimane)
  - Per quanto tempo si prevede di conservare i backup mensili? (in mesi)
  - Per quanto tempo si prevede di conservare i backup annuali? (in anni)
- **Facoltativo** - Ridondanza dell'archivio di backup
  
  - Indica la ridondanza dell'account di archiviazione in cui andranno i dati di backup. È consigliabile usare l'opzione **Archiviazione con ridondanza geografica** per la massima disponibilità. Con questa opzione una copia dei dati viene mantenuta in un'area diversa ed è quindi possibile soddisfare più standard di conformità. Cambiare la ridondanza in **Archiviazione con ridondanza locale** se il backup riguarda ambienti di sviluppo o di test e non è necessario eseguirlo a livello aziendale.
- **Facoltativo** - Modificare i prezzi a livello di area o applicare tariffe scontate
  
  - Per controllare le stime per un'area diversa o per tariffe scontate, selezionare **Sì** per l'opzione **Try estimates for a different region?** (Provare le stime per un'area diversa?) e immettere le tariffe con cui eseguire le stime.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Stimare i costi per il backup delle condivisioni file di Azure

Per stimare i costi del backup delle condivisioni file di Azure usando la [soluzione di backup basata su snapshot](azure-file-share-backup-overview.md) offerta da Backup di Azure, sono necessari i parametri seguenti:

- Dimensioni ( **in GB** ) delle condivisioni file di cui eseguire il backup.

- Per eseguire il backup di condivisioni file distribuite tra più account di archiviazione, specificare il numero di account di archiviazione che ospitano le condivisioni file con le suddette dimensioni.

- Entità prevista della varianza dei dati nelle condivisioni file di cui eseguire il backup. <br>La varianza si riferisce alla quantità di modifiche nei dati e influisce direttamente sulle dimensioni di archiviazione degli snapshot. Se ad esempio una condivisione file contiene 200 GB di dati per il backup di cui 10 GB cambiano ogni giorno, la varianza giornaliera sarà del 5%.
  - Una varianza più elevata indica che la quantità di modifiche dei dati nel contenuto della condivisione file è ogni giorno elevata e quindi saranno maggiori anche le dimensioni incrementali degli snapshot (che acquisiscono solo le modifiche dei dati).
  - Selezionare Bassa (1%), Moderata (3%) o Alta (5%) in base alle caratteristiche e all'utilizzo della condivisione file.
  - Se si conosce la percentuale esatta di varianza per la condivisione file, è possibile selezionare l'opzione **Enter your own%** (Immettere una percentuale personalizzata) nell'elenco a discesa. Specificare i valori (in percentuale) per la varianza giornaliera, settimanale, mensile e annuale.

- Il tipo di account di archiviazione (Standard o Premium) e l'impostazione di ridondanza di archiviazione dell'account di archiviazione che ospita i backup della condivisione file. <br>Nella soluzione di backup corrente per le condivisioni file di Azure, gli snapshot vengono archiviati nello stesso account di archiviazione della condivisione file di cui viene eseguito il backup. Quindi i costi dell'archiviazione associati agli snapshot vengono fatturati come parte della fattura dei file di Azure, in base ai prezzi degli snapshot per il tipo di account e l'impostazione della ridondanza dell'account di archiviazione che ospita i backup della condivisione file e degli snapshot.

- Conservazione per backup diversi
  - Per quanto tempo si prevede di conservare i backup giornalieri? (in giorni)
  - Per quanto tempo si prevede di conservare i backup settimanali? (in settimane)
  - Per quanto tempo si prevede di conservare i backup mensili? (in mesi)
  - Per quanto tempo si prevede di conservare i backup annuali? (in anni)

  Per informazioni sui valori massimi supportati per la conservazione in ogni categoria, vedere la [matrice di supporto per la condivisione file di Azure](azure-file-share-support-matrix.md#retention-limits).

- **Facoltativo** - Modificare i prezzi a livello di area o applicare tariffe scontate.
  - I valori predefiniti per i costi dell'archiviazione di snapshot per GB e i costi delle istanze protette nello strumento di stima fanno riferimento all'area Stati Uniti orientali. Per controllare le stime per un'area diversa o per tariffe scontate, selezionare **Sì** per l'opzione **Try estimates for a different region?** (Provare le stime per un'ara diversa?) e immettere le tariffe con cui eseguire le stime.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul servizio Backup di Azure](backup-overview.md)
