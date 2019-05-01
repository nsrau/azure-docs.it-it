---
title: Personalizzazione dei criteri di protezione delle informazioni SQL in Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come personalizzare i criteri di protezione delle informazioni in Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: rkarlin
ms.openlocfilehash: e2bcd92ffc8799b2edf04251b6ce160e96c7609c
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875597"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalizzare i criteri di protezione delle informazioni SQL in Centro sicurezza di Azure (Anteprima)
 
Un criterio di protezione delle informazioni SQL può essere definito e personalizzato per l'intero tenant di Azure in Centro sicurezza di Azure.

Information Protection è una funzionalità di sicurezza avanzata per l'individuazione, la classificazione, l'assegnazione di etichette e la protezione dei dati sensibili nelle risorse dati di Azure. L'individuazione e la classificazione dei dati più sensibili (dati commerciali, finanziari e relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:
- Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati
- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili
- Controllare l'accesso ai database che contengono dati molto sensibili e rafforzarne la sicurezza
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementa questo paradigma per gli archivi dati SQL, attualmente supportati per il database SQL di Azure. SQL Information Protection consente di individuare e classificare automaticamente i dati potenzialmente sensibili, offre un meccanismo di assegnazione delle etichette per contrassegnare in modo permanente i dati sensibili con attributi di classificazione e contiene un dashboard dettagliato che indica lo stato di classificazione del database. Inoltre, calcola la riservatezza del set di risultati delle query SQL, in modo che le query che estraggono dati sensibili possano essere controllate in modo esplicito, e i dati protetti. Per altre informazioni su SQL Information Protection, vedere [Individuazione dati e classificazione nel database SQL di Azure](../sql-database/sql-database-data-discovery-and-classification.md).
 
Il meccanismo di classificazione si basa su due costrutti principali che costituiscono la tassonomia di classificazione, cioè le **etichette** e i **tipi di informazioni**.
- **Etichette**: i principali attributi di classificazione, usati per definire il livello di sensibilità dei dati archiviati nella colonna. 
- **Tipi di informazioni**: forniscono granularità aggiuntiva al tipo di dati archiviati nella colonna.
 
Information Protection include un set predefinito di etichette e i tipi di informazioni, che viene utilizzato per impostazione predefinita. Per personalizzarlo, è necessario personalizzare i criteri di protezione delle informazioni in Centro sicurezza di Azure.
 
## <a name="customize-the-information-protection-policy"></a>Personalizzare i criteri di protezione delle informazioni
Per personalizzare i criteri di protezione delle informazioni del tenant di Azure, è necessario disporre di [privilegi amministrativi nel gruppo di gestione radice del tenant](security-center-management-groups.md). 
 
1. Della sicurezza nel centro menu principale, in **risorsa sicurezza igiene** passare alla **dati e archiviazione** e fare clic sul **SQL Information Protection** pulsante.

   ![Configurare i criteri di protezione delle informazioni](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Nel **SQL Information Protection** pagina, è possibile visualizzare il set corrente di etichette. Questi sono gli attributi di classificazione principali che consentono di classificare il livello di riservatezza dei dati. A questo punto, è possibile configurare le **etichette di Information Protection** e i **tipi di informazioni** per il tenant. 
 
### <a name="customizing-labels"></a>Personalizzazione delle etichette
 
1. È possibile modificare o eliminare qualsiasi etichetta esistente o aggiungere una nuova etichetta. Per modificare un'etichetta esistente, selezionarla e fare clic su **Configura** nella parte superiore o nel menu di scelta rapida a destra. Per aggiungere una nuova etichetta, fare clic su **Crea etichetta** nella barra dei menu superiore o in fondo alla tabella delle etichette.
2. Nella schermata **Configura l'etichetta di riservatezza**  è possibile creare o modificare il nome dell'etichetta e la descrizione. È anche possibile specificare se l'etichetta è abilitata o disabilitata attivando o disattivando il comando **Abilitata**. Infine, è possibile aggiungere o rimuovere i tipi di informazioni associati all'etichetta. Tutti i dati rilevati che corrispondono al tipo di informazioni includerà automaticamente l'etichetta di riservatezza associata tra i consigli per la classificazione.
3. Fare clic su **OK**.
 
   ![Configurare l'etichetta di riservatezza](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Le etichette sono elencate in ordine crescente di riservatezza. Per modificare la classificazione di due etichette, trascinare le etichette in una posizione diversa della tabella oppure usare i pulsanti **Sposta su** e **Sposta giù** per modificare l'ordine. 
 
    ![Configurare i criteri di protezione delle informazioni](./media/security-center-info-protection-policy/move-up.png)
 
5. Alla fine, assicurarsi di fare clic su **Salva** nella parte superiore della schermata.
 
 
## <a name="adding-and-customizing-information-types"></a>Aggiunta e personalizzazione dei tipi di informazioni
 
1. È possibile gestire e personalizzare i tipi di informazioni facendo clic su **Gestisci i tipi di informazioni**.
2. Per aggiungere un nuovo **Tipo di informazioni**, selezionare **Crea tipo di informazioni** nel menu in alto. Per il **Tipo di informazioni** è possibile configurare un nome, una descrizione e le stringhe dei criteri di ricerca. Le stringhe dei criteri di ricerca possono contenere facoltativamente parole chiave con caratteri jolly, indicati dal carattere '%', che il motore di rilevamento automatico usa per identificare i dati sensibili nei database in base ai metadati delle colonne.
 
    ![Configurare i criteri di protezione delle informazioni](./media/security-center-info-protection-policy/info-types.png)
 
3. È anche possibile configurare i **tipi di informazioni** incorporati mediante l'aggiunta di stringhe dei criteri di ricerca, la disabilitazione di alcune stringhe esistenti o la modifica della descrizione. Non è possibile eliminare i **tipi di informazioni** incorporati o modificarne i nomi. 
4. I **tipi di informazioni** sono elencati in ordine di classificazione dell'individuazione decrescente, il che significa che i tipi più in alto nell'elenco tenteranno di trovare una corrispondenza prima dei tipi seguenti. Per modificare la classificazione di due tipi, trascinare i tipi nel punto desiderato della tabella oppure usare i pulsanti **Sposta su** e **Sposta giù** per modificare l'ordine. 
5. Al termine, fare clic su **OK**.
6. Dopo aver completato la gestione dei tipi di informazioni, associare i tipi alle etichette pertinenti facendo clic su **Configura** per una determinata etichetta e aggiungendo o eliminando i tipi di informazioni da associare a essa.
7. Fare clic su **Salva** nel pannello **Etichette** principale per applicare tutte le modifiche.
 
Dopo che è stato definito e salvato, il criterio di Information Protection verrà applicato alla classificazione dei dati in tutti i database SQL di Azure nel tenant.
 
 
## <a name="next-steps"></a>Passaggi successivi
 
In questo articolo è stata illustrata la definizione di un criterio di Information Protection per SQL nel Centro sicurezza di Azure. Per altre informazioni sull'utilizzo di SQL Information Protection per classificare e proteggere i dati sensibili nel database SQL, vedere [Individuazione dati e classificazione nel database SQL di Azure](../sql-database/sql-database-data-discovery-and-classification.md). 

Per altre informazioni sui criteri di sicurezza e la protezione dei dati nel Centro sicurezza di Azure, vedere gli articoli seguenti:
 
- [Panoramica dei criteri di sicurezza](security-center-policies-overview.md): una panoramica dei criteri di sicurezza del Centro sicurezza.
- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni su come il Centro sicurezza gestisce e protegge i dati


