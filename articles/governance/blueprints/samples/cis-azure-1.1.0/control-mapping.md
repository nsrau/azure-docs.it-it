---
title: Esempio - Progetto CIS Microsoft Azure Foundations Benchmark - Mapping di raccomandazioni
description: Mapping di raccomandazioni del progetto di esempio CIS Microsoft Azure Foundations Benchmark in Criteri di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f048262003a3567175c40ebf4ee744c41e11b5f9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918704"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapping di raccomandazioni del progetto di esempio CIS Microsoft Azure Foundations Benchmark

L'articolo seguente illustra nel dettaglio come l'esempio di progetto Azure Blueprints CIS Microsoft Azure Foundations Benchmark esegue il mapping alle raccomandazioni specifiche di CIS Microsoft Azure Foundations Benchmark. Per altre informazioni sulle raccomandazioni, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Di seguito sono riportati i mapping alle raccomandazioni di **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping di raccomandazioni.
Molte raccomandazioni mappate vengono implementate con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica raccomandazioni CIS Microsoft Azure Foundations Benchmark v1.1.1.0 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti con privilegi

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che si allineano a questa raccomandazione di CIS.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti senza privilegi

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Assicurarsi che non siano presenti utenti Guest

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che si allineano a questa raccomandazione di CIS.

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora aggiornamenti del sistema" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Gli aggiornamenti di sistema devono essere installati nelle macchine

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora vulnerabilità del sistema operativo" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora protezione endpoint" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia disco" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora web application firewall" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le regole per i gruppi di sicurezza di rete delle applicazioni Web in IaaS devono essere rafforzate

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora la valutazione della vulnerabilità" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora accesso JIT alla rete" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia SQL" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Assicurarsi che l'opzione "Trasferimento sicuro obbligatorio" sia impostata su "Abilitato".

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Il trasferimento sicuro negli account di archiviazione deve essere abilitato

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Assicurarsi che la regola di accesso alla rete predefinita per gli account di archiviazione sia impostata su Nega

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Assicurarsi che "Controllo" sia impostato su "Attivato"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Assicurarsi che "AuditActionGroups" nel criterio "controllo" per un server SQL sia impostato correttamente

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le impostazioni di controllo SQL devono avere gruppi di azione configurati per acquisire attività critiche

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Assicurarsi che il periodo di conservazione dei dati di controllo sia "superiore a 90 giorni"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- È consigliabile che i server SQL devono siano configurati con un periodo di conservazione dei dati di controllo superiore a 90 giorni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Assicurarsi che "Sicurezza dei dati avanzata" in un server SQL sia impostata su "Attivata"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 assicurarsi che "Tipi di rilevamento minacce" sia impostato su "Tutti"

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che si allineano a questa raccomandazione di CIS.

- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata nell'istanza gestita di SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 assicurarsi che "Invia avvisi a" sia impostato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Assicurarsi che "Invio di un messaggio di posta elettronica al servizio e ai coamministratori" sia "Abilitato"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Assicurarsi che l'amministratore di Azure Active Directory sia configurato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Assicurarsi che "Crittografia dati" sia impostato su "Attivato" in un database SQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Assicurarsi che la protezione TDE di SQL Server sia crittografata con BYOK (Bring Your Own Key)

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che si allineano a questa raccomandazione di CIS.

- È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Assicurarsi che la registrazione per l'insieme di credenziali delle credenziali di Azure sia abilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- I log di diagnostica in Key Vault devono essere abilitati

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Assicurarsi che "disco del sistema operativo" sia crittografato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Assicurarsi che i "dischi dati" siano crittografati

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Assicurarsi che vengano applicate le patch più recenti del sistema operativo per tutte le macchine virtuali

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Gli aggiornamenti di sistema devono essere installati nelle macchine

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Assicurarsi che venga installata la protezione endpoint per tutte le Macchine virtuali di Microsoft Azure

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Abilitare il controllo degli accessi in base al ruolo con il servizio Azure Kubernetes

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- \[Anteprima\]: il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Assicurarsi che l'app Web reindirizzi tutto il traffico HTTP a HTTPS nel servizio app Azure

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che si allinea a questa raccomandazione di CIS.

- L'applicazione Web deve essere accessibile solo tramite HTTPS

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato esaminato il mapping dei controlli del progetto CIS Microsoft Azure Foundations Benchmark, vedere l'articolo seguente per informazioni sul progetto o visitare Criteri di Azure nel portale di Azure per assegnare l'iniziativa:

> [!div class="nextstepaction"]
> [Progetto CIS Microsoft Azure Foundations Benchmark - Panoramica](./index.md)
> [Portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).