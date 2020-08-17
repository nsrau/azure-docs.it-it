---
title: Panoramica degli insiemi di credenziali di Servizi di ripristino
description: Una panoramica e un confronto tra gli insiemi di credenziali di Servizi di ripristino e gli insiemi di credenziali di Backup di Azure.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2b292a39e38ef5e298f45c2babbee9fbd20c39ea
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261872"
---
# <a name="recovery-services-vaults-overview"></a>Panoramica di insiemi di credenziali di Servizi di ripristino

In questo articolo vengono descritte le funzionalità di un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali di Servizi di ripristino è un'entità di archiviazione di Azure che ospita i dati. I dati sono in genere copie di dati o informazioni di configurazione per macchine virtuali, carichi di lavoro, server o workstation. È possibile usare gli insiemi di credenziali di Servizi di ripristino per contenere dati di backup per vari servizi di Azure, ad esempio database SQL di Azure e macchine virtuali IaaS, Linux o Windows. Gli insiemi di credenziali di Servizi di ripristino supportano System Center DPM, Windows Server, server di Backup di Azure e altro ancora. Gli insiemi di credenziali di Servizi di ripristino semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione. Gli insiemi di credenziali dei servizi di ripristino sono basati sul modello di Azure Resource Manager di Azure, che offre funzionalità come:

- **Funzionalità avanzate per proteggere i dati di backup**: con gli insiemi di credenziali di Servizi di ripristino, Backup di Azure offre funzionalità di sicurezza per proteggere i backup nel cloud. Queste funzionalità di sicurezza garantiscono la protezione dei backup e ripristinano i dati in modo sicuro anche se i server di produzione e di backup vengono compromessi. [Altre informazioni](backup-azure-security-feature.md)

- **Monitoraggio centralizzato per l'ambiente IT ibrido**: con gli insiemi di credenziali di Servizi di ripristino, è possibile monitorare non solo le [macchine virtuali IaaS di Azure](backup-azure-manage-vms.md) ma anche le [risorse locali](backup-azure-manage-windows-server.md#manage-backup-items) da un portale centrale. [Altre informazioni](backup-azure-monitoring-built-in-monitor.md)

- **Controllo degli accessi in base al ruolo o RBAC **: il controllo degli accessi in base al ruolo consente un controllo della gestione degli accessi con granularità fine in Azure. [Azure offre diversi ruoli predefiniti](../role-based-access-control/built-in-roles.md) mentre Backup di Azure dispone di tre [ruoli predefiniti per la gestione dei punti di ripristino](backup-rbac-rs-vault.md). Gli insiemi di credenziali di Servizi di ripristino sono compatibili con il controllo degli accessi in base al ruolo, che consente di limitare il backup e ripristinare l'accesso a insiemi definiti di ruoli utente. [Altre informazioni](backup-rbac-rs-vault.md)

- **Eliminazione**temporanea: con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione per i dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente. [Altre informazioni](backup-azure-security-feature-cloud.md)

- **Ripristino tra più aree**: il ripristino tra più aree (CRR) consente di ripristinare macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata ad Azure. Se Azure dichiara un'emergenza nell'area primaria, i dati replicati nell'area secondaria sono disponibili per il ripristino nell'area secondaria per attenuare l'emergenza del tempo di inattività reale nell'area primaria per il proprio ambiente. [Altre informazioni](backup-azure-arm-restore-vms.md#cross-region-restore)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Impostazioni di archiviazione nell'insieme di credenziali di servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

- Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. Vedere come [è possibile modificare le impostazioni di archiviazione](./backup-create-rs-vault.md#set-storage-redundancy).

- Per altre informazioni sulla ridondanza di archiviazione, vedere questi articoli sulla ridondanza [geografica](../storage/common/storage-redundancy.md) e [locale](../storage/common/storage-redundancy.md) .

### <a name="additional-resources"></a>Risorse aggiuntive

- [Scenari supportati e non supportati per l'insieme di credenziali](backup-support-matrix.md#vault-support)
- [Domande frequenti sull'insieme di credenziali](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) è un consulente cloud personalizzato che consente di ottimizzare l'uso di Azure. Analizza l'utilizzo di Azure e fornisce indicazioni tempestive per ottimizzare e proteggere le distribuzioni. Fornisce consigli in quattro categorie: disponibilità elevata, sicurezza, prestazioni e costi.

Azure Advisor fornisce le [raccomandazioni](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) orarie per le macchine virtuali di cui non è stato eseguito il backup, quindi non è possibile eseguire il backup di macchine virtuali importanti. È anche possibile controllare le raccomandazioni eseguendone la ripetizione.  È possibile selezionare la raccomandazione e abilitare il backup sulle VM inline specificando l'insieme di credenziali (in cui verranno archiviati i backup) e i criteri di backup (pianificazione dei backup e conservazione delle copie di backup).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:</br>
[Eseguire il backup di una macchina virtuale IaaS](backup-azure-arm-vms-prepare.md)</br>
[Eseguire il backup del server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</br>
[Eseguire il backup di Windows Server](backup-windows-with-mars-agent.md)
