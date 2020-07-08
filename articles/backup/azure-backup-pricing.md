---
title: Prezzi di Backup di Azure
description: Informazioni su come stimare i costi per il budget dei prezzi di backup di Azure.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 274a61ff5a98fa1291f9d8917af9ab1d1b3da2fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391112"
---
# <a name="azure-backup-pricing"></a>Prezzi di Backup di Azure

Per informazioni sui prezzi di backup di Azure, visitare la [pagina dei prezzi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Scarica le stime dettagliate per i prezzi di backup di Azure

Se si vuole stimare i costi per i costi di budget o di confronto dei costi, scaricare lo strumento di [stima dei prezzi di backup di Azure](https://aka.ms/AzureBackupCostEstimates)dettagliato.  

### <a name="what-does-the-estimator-contain"></a>Che cosa contiene lo strumento di stima?

Il foglio di stima dei costi di backup di Azure offre un'opzione che consente di stimare tutti i carichi di lavoro di cui si sta cercando di eseguire il backup con backup di Azure. Questi carichi di lavoro includono:

- Macchine virtuali di Azure
- Server locali
- SQL in macchine virtuali di Azure
- SAP HANA in macchine virtuali di Azure
- Condivisioni file di Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Stimare i costi per il backup di macchine virtuali di Azure o server locali

Per stimare i costi del backup di macchine virtuali di Azure o server locali con backup di Azure, sono necessari i parametri seguenti:

- Dimensioni delle macchine virtuali o dei server locali di cui si sta provando a eseguire il backup
  - Immettere le "dimensioni utilizzate" dei dischi o dei server di cui eseguire il backup

- Numero di server con tali dimensioni

- Qual è la quantità prevista di varianza dei dati in questi server?<br>
  La varianza si riferisce alla quantità di modifiche nei dati. Se, ad esempio, si dispone di una macchina virtuale con 200 GB di dati di cui eseguire il backup e se 10 GB cambiano ogni giorno, la varianza giornaliera sarà pari al 5%.

  - Una varianza più elevata comporta l'esecuzione del backup di più dati

  - Scegli **bassa** o **moderata** per i file server e **alta** se Esegui i database

  - Se si conosce la **varianza%**, è possibile usare l'opzione **immettere il proprio%**

- Scegliere i criteri di backup

  - Quanto tempo si prevede di mantenere i backup giornalieri? (in giorni)

  - Quanto tempo si prevede di mantenere i backup "settimanali"? (in settimane)

  - Quanto tempo si prevede di mantenere i backup "mensili"? (in mesi)

  - Quanto tempo si prevede di mantenere i backup "annuali"? (in anni)

  - Quanto tempo si prevede di mantenere "snapshot di ripristino istantaneo"? (1-5 giorni)

    - Questa opzione consente di eseguire il ripristino da fino a sette giorni in modo rapido usando gli snapshot archiviati nei dischi

- **Facoltativo** : backup del disco selettivo

  - Se si usa l'opzione di **backup su disco selettivo** durante il backup di macchine virtuali di Azure, scegliere l'opzione **Escludi disco** e immettere la percentuale di dischi esclusi dal backup in termini di dimensioni. Ad esempio, se si dispone di una macchina virtuale connessa a tre dischi con 200 GB usati in ogni disco e se si desidera escluderne due dal backup, immettere 66,7%.

- **Facoltativo** : ridondanza dell'archiviazione di backup

  - Indica la ridondanza dell'account di archiviazione in cui passano i dati di backup. È consigliabile usare **GRS** per la massima disponibilità. Poiché garantisce che una copia dei dati di backup venga mantenuta in un'area diversa, consente di soddisfare più standard di conformità. Modificare la ridondanza in **con ridondanza locale** se si esegue il backup di ambienti di sviluppo o test che non necessitano di un backup a livello aziendale. Selezionare l'opzione **RAGRS** nel foglio se si desidera comprendere i costi quando il [ripristino tra aree](backup-azure-arm-restore-vms.md#cross-region-restore) è abilitato per i backup.

- **Facoltativo** : modificare i prezzi regionali o applicare tariffe scontate

  - Se si desidera controllare le stime per un'area diversa o tariffe scontate, selezionare **Sì** per l'opzione **try estimates for an different region?** e immettere le tariffe con cui si desidera eseguire le stime.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Stimare i costi per il backup di SQL Server in macchine virtuali di Azure

Per stimare i costi del backup di SQL Server in esecuzione in macchine virtuali di Azure con backup di Azure, sono necessari i parametri seguenti:

- Dimensioni dei server SQL di cui si sta tentando di eseguire il backup

- Numero di server SQL con le dimensioni precedenti

- Qual è la compressione prevista per i dati di backup di SQL Server?

  - La maggior parte dei clienti di backup di Azure vede che i dati di backup hanno una compressione del 80% rispetto alle dimensioni di SQL Server quando la compressione SQL è **abilitata**.

  - Se si prevede di visualizzare una compressione diversa, immettere il numero in questo campo

- Quali sono le dimensioni previste dei backup del log?

  - % Indica le dimensioni del log giornaliero come percentuale delle dimensioni di SQL Server

- Qual è la quantità prevista di varianza dei dati giornaliera su questi server?

  - In genere, i database hanno una varianza "elevata"

  - Se si conosce la **varianza%**, è possibile usare l'opzione **immettere il proprio%**

- Scegliere i criteri di backup

  - Tipo di backup

    - I criteri più efficaci che è possibile scegliere sono i **differenziali giornalieri** con backup completi settimanali/mensili/annuali. Backup di Azure può essere ripristinato anche da differenziali tramite un singolo clic.

    - È anche possibile scegliere di disporre di un criterio con backup completi giornalieri/settimanali/mensili/annuali. Questa opzione utilizzerà una quantità di spazio di archiviazione leggermente superiore a quella della prima opzione.

  - Quanto tempo si prevede di mantenere i backup "log"? (in giorni) [7-35]

  - Quanto tempo si prevede di mantenere i backup giornalieri? (in giorni)

  - Quanto tempo si prevede di mantenere i backup "settimanali"? (in settimane)

  - Quanto tempo si prevede di mantenere i backup "mensili"? (in mesi)

  - Quanto tempo si prevede di mantenere i backup "annuali"? (in anni)

- **Facoltativo** : ridondanza dell'archiviazione di backup

  - Indica la ridondanza dell'account di archiviazione in cui passano i dati di backup. È consigliabile usare **GRS** per la massima disponibilità. Poiché garantisce che una copia dei dati di backup venga mantenuta in un'area diversa, consente di soddisfare più standard di conformità. Modificare la ridondanza in **con ridondanza locale** se si esegue il backup di ambienti di sviluppo o test che non necessitano di un backup a livello aziendale.

- **Facoltativo** : modificare i prezzi regionali o applicare tariffe scontate

  - Se si desidera controllare le stime per un'area diversa o tariffe scontate, selezionare **Sì** per l'opzione **try estimates for an different region?** e immettere le tariffe con cui si desidera eseguire le stime.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Stimare i costi per il backup di SAP HANA server in macchine virtuali di Azure

Per stimare i costi del backup di SAP HANA server in esecuzione in macchine virtuali di Azure con backup di Azure, sono necessari i parametri seguenti:

- Dimensioni totali dei database di SAP HANA di cui si sta provando a eseguire il backup. Deve corrispondere alla somma delle dimensioni del backup completo di ogni database, come riportato da SAP HANA.
- Numero di server di SAP HANA con le dimensioni precedenti
- Quali sono le dimensioni previste dei backup del log?
  - % Indica la dimensione media giornaliera del log come percentuale della dimensione totale dei database di SAP HANA di cui si sta eseguendo il backup nel server SAP HANA
- Qual è la quantità prevista di varianza dei dati giornaliera su questi server?
  - % Indica una dimensione della varianza giornaliera media come percentuale della dimensione totale dei database di SAP HANA di cui si sta eseguendo il backup nel server SAP HANA
  - In genere, i database hanno una varianza "elevata"
  - Se si conosce la **varianza%**, è possibile usare l'opzione **immettere il proprio%**
- Scegliere i criteri di backup
  - Tipo di backup
    - I criteri più efficaci che è possibile scegliere sono i **differenziali giornalieri** con backup completi **settimanali/mensili/annuali** . Backup di Azure può essere ripristinato anche da differenziali tramite un singolo clic.
    - È anche possibile scegliere di disporre di un criterio con backup completi **giornalieri/settimanali/mensili/annuali** . Questa opzione utilizzerà una quantità di spazio di archiviazione leggermente superiore a quella della prima opzione.
  - Quanto tempo si prevede di mantenere i backup "log"? (in giorni) [7-35]
  - Quanto tempo si prevede di mantenere i backup giornalieri? (in giorni)
  - Quanto tempo si prevede di mantenere i backup "settimanali"? (in settimane)
  - Quanto tempo si prevede di mantenere i backup "mensili"? (in mesi)
  - Quanto tempo si prevede di mantenere i backup "annuali"? (in anni)
- **Facoltativo** : ridondanza dell'archiviazione di backup
  - Indica la ridondanza dell'account di archiviazione in cui passano i dati di backup. È consigliabile usare **GRS** per la massima disponibilità. Poiché garantisce che una copia dei dati di backup venga mantenuta in un'area diversa, consente di soddisfare più standard di conformità. Modificare la ridondanza in **con ridondanza locale** se si esegue il backup di ambienti di sviluppo o test che non necessitano di un backup a livello aziendale.
- **Facoltativo** : modificare i prezzi regionali o applicare tariffe scontate
  - Se si desidera controllare le stime per un'area diversa o tariffe scontate, selezionare **Sì** per l'opzione **try estimates for an different region?** e immettere le tariffe con cui si desidera eseguire le stime.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul servizio Backup di Azure](backup-overview.md)
