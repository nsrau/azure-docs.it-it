<properties
   pageTitle="Abilitare il controllo sui database SQL nel Centro sicurezza di Azure | Microsoft Azure"
   description="In questo documento è illustrato come implementare la raccomandazione **Abilitare il controllo sui database SQL** del Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/27/2016"
   ms.author="terrylan"/>

# Abilitare il controllo sui database SQL nel Centro sicurezza di Azure

Il Centro sicurezza di Azure consiglia di attivare il controllo per tutti i database SQL, se non è già abilitato. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Dopo aver attivato il controllo è possibile configurare le impostazioni di rilevamento delle minacce e gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzionalità di rilevamento delle minacce individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. Essa consente di rilevare e rispondere a potenziali rischi appena si verificano.

Questa indicazione si applica esclusivamente al servizio SQL di Azure e non include SQL in esecuzione sulle macchine virtuali.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Implementare la raccomandazione

1. Nel pannello **Raccomandazioni** selezionare **Abilita il controllo sui database SQL**. Verrà visualizzato il pannello **Abilita il controllo sui database SQL**. ![][1]

2. Selezionare un database SQL su cui abilitare il controllo. Viene visualizzato il pannello **Controllo e rilevamento minacce**. ![][2]
3. Nel pannello **Controllo e rilevamento minacce** selezionare **ON** in **Controllo**. ![][3]

4. Seguire la procedura in [Introduzione al controllo del database SQL](../sql-database/sql-database-auditing-get-started.md) per configurare l'archiviazione in cui verranno archiviati i log di controllo. L'account di archiviazione della sottoscrizione per la raccolta dei dati è l'account di archiviazione predefinito.

5. Seguire i passaggi in [Introduzione individuazione Database SQL](../sql-database/sql-database-threat-detection-get-started.md) per attivare e configurare il rilevamento di minacce e per configurare l'elenco dei messaggi di posta elettronica che riceveranno gli avvisi di sicurezza al rilevamento di anomalie dell'attività.

## Passaggi successivi

Questo documento illustra come implementare la raccomandazione "Abilitare il controllo sui database SQL" del Centro sicurezza. Per altre informazioni su come proteggere il database SQL, vedere gli argomenti seguenti:

- [Protezione del Database SQL](../sql-database/sql-database-security.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

<!---HONumber=AcomDC_0629_2016-->