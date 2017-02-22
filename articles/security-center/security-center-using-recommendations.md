---
title: Uso delle raccomandazioni del Centro sicurezza di Azure per migliorare la sicurezza | Microsoft Docs
description: " Imparare a usare i criteri di sicurezza e le raccomandazioni nel Centro sicurezza di Azure per attenuare un attacco alla sicurezza. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 313801fc3b845cd30506b1a26918660e77d2a986
ms.openlocfilehash: 67095e0d8752fb3a2bca692f65e77cde5fd1a966


---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Uso delle raccomandazioni del Centro sicurezza di Azure per migliorare la sicurezza
Configurando i criteri di sicurezza e implementando le raccomandazioni offerte dal Centro sicurezza di Azure, è possibile ridurre le probabilità di un importante evento di sicurezza. Questo articolo illustra come usare i criteri di sicurezza e le raccomandazioni nel Centro sicurezza di Azure per attenuare un attacco alla sicurezza.

> [!NOTE]
> Questo articolo si basa sui ruoli e i concetti introdotti nella [guida alla pianificazione e gestione](security-center-planning-and-operations-guide.md) del Centro sicurezza. Prima di continuare è consigliabile esaminare la guida alla pianificazione.
>
>

## <a name="managing-security-recommendations"></a>Gestione delle raccomandazioni sulla sicurezza
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse nell'ambito della sottoscrizione o del gruppo di risorse specificato. Nel Centro sicurezza è possibile definire i criteri in base ai requisiti di sicurezza della società. Per altre informazioni, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

I criteri di sicurezza per i gruppi di risorse vengono ereditati dal livello della sottoscrizione.

![Ereditarietà dei criteri di sicurezza][1]

Se sono necessari criteri personalizzati in gruppi di risorse specifici, è possibile disabilitare l'ereditarietà nel gruppo di risorse. Per disabilitare, impostare Ereditarietà su Univoca nel pannello di criteri di sicurezza e personalizzare i controlli per cui il Centro sicurezza mostra le raccomandazioni.

Ad esempio, se alcuni carichi di lavoro non richiedono il criterio Transparent Data Encryption (TDE) del database SQL, occorre disabilitarlo al livello di sottoscrizione e abilitarlo solo nei gruppi di risorse in cui è richiesto.

> [!NOTE]
> In caso di conflitto tra criteri definiti a livello di sottoscrizione e quelli a livello di gruppo di risorse, i criteri a livello di gruppo di risorse avranno la precedenza.
>
>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni in base ai controlli impostati nel criterio di sicurezza. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli di sicurezza necessari.

Le raccomandazioni correnti dei criteri nel Centro sicurezza sono incentrate sugli aggiornamenti di sistema, sulla configurazione del sistema operativo, sui gruppi di sicurezza di rete su subnet e sulle macchine virtuali (VM), sul controllo del Database SQL, sul criterio TDE del Database SQL e sui firewall dell'applicazione web. Per informazioni più aggiornate sulle raccomandazioni del Centro sicurezza, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

## <a name="scenario"></a>Scenario
In questo scenario viene illustrato come utilizzare il Centro sicurezza per ridurre il rischio di importanti problemi di sicurezza monitorando e intervenendo sulle raccomandazioni del Centro sicurezza. Si fa riferimento a una società fittizia, Contoso, e ai ruoli presentati nella [guida alla pianificazione e gestione](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) del Centro sicurezza. I ruoli rappresentano singoli utenti e gruppi che usano il Centro sicurezza per eseguire diverse attività correlate alla sicurezza. I ruoli sono:

![Ruoli dello scenario][2]

Contoso di recente ha eseguito la migrazione di alcune delle proprie risorse locali in Azure. Contoso desidera implementare e applicare protezioni che riducano le vulnerabilità dell'azienda in caso di un attacco alle proprie risorse nel cloud.

## <a name="recommended-solution"></a>Soluzione consigliata
Una soluzione consiste nell'utilizzare il Centro sicurezza per prevenire e rilevare vulnerabilità della sicurezza. Contoso ha accesso al Centro sicurezza grazie alla sottoscrizione di Azure. In tutte le sottoscrizioni di Azure viene abilitato automaticamente il [livello gratuito](security-center-pricing.md) del Centro sicurezza e su tutte le macchine virtuali della propria sottoscrizione viene abilitata la raccolta dei dati.

David, del reparto di sicurezza informatica di Contoso, configura i **criteri di sicurezza** con il Centro sicurezza. Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure di Contoso. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea **raccomandazioni** in base ai controlli impostati nel criterio di sicurezza.

Jeff, un proprietario del carico di lavoro su cloud, è responsabile delle attività di implementazione e gestione della protezione secondo i criteri di sicurezza di Contoso. Jeff può monitorare le raccomandazioni create dal Centro sicurezza per applicare le protezioni. Queste raccomandazioni guidano Jeff nel processo di configurazione dei controlli di sicurezza necessari.

Affinché Jeff implementi e gestisca le protezioni ed elimini le vulnerabilità di sicurezza, deve:

- monitorare le raccomandazioni di sicurezza presenti nel Centro sicurezza
- valutare le raccomandazioni sulla sicurezza e decidere se applicarle o ignorarle
- applicare le raccomandazioni sulla sicurezza

Seguiamo la procedura di Jeff per vedere come usa le raccomandazioni del Centro sicurezza per configurare i controlli al fine di eliminare le vulnerabilità della sicurezza.

## <a name="how-to-implement-this-solution"></a>Come implementare questa soluzione
Jeff effettua l'accesso al [Portale di Azure](https://azure.microsoft.com/features/azure-portal/) e apre la console del Centro sicurezza. Come parte delle sue attività di monitoraggio giornaliere, controlla se ci sono raccomandazioni sulla sicurezza eseguendo la procedura seguente:

1. Jeff seleziona il riquadro **Raccomandazioni** per aprire il pannello **Raccomandazioni**.
   ![Selezionare il riquadro Raccomandazioni][3]
2. Jeff esamina l'elenco delle raccomandazioni. Nota che il Centro sicurezza presenta l'elenco delle raccomandazioni in ordine di priorità, dalla priorità più alta a quella più bassa. Decide di risolvere la prima raccomandazioni con priorità alta nell'elenco. Nel pannello **Raccomandazioni** seleziona **Installa Endpoint Protection**.
3. Viene visualizzato il pannello **Installa Endpoint Protection** che mostra un elenco di macchine virtuali per cui non è abilitato l'antimalware. Jeff controlla l'elenco delle macchine virtuali, seleziona tutte le macchine virtuali e quindi seleziona **Installa nelle VM 3**.
   ![Installare Endpoint Protection][4]
4. Viene visualizzato il pannello **Select Endpoint Protection** (Seleziona Endpoint Protection) che offre a Jeff due soluzioni antimalware. Jeff seleziona la soluzione **Microsoft Antimalware**.
5. Vengono visualizzate altre informazioni sulla soluzione antimalware selezionata. Jeff seleziona **Crea**.
   ![Microsoft antimalware][5]
6. Jeff inserisce le impostazioni di configurazione richieste nel pannello **Installazione** e seleziona **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) è ora attivo nelle VM selezionate.

Jeff continua sfoglia le raccomandazioni con priorità alta e media, decidendo di volta in volta sull'implementazione. Jeff fa riferimento all'articolo sulla [gestione delle raccomandazioni sulla sicurezza](security-center-recommendations.md) per capire cosa sono le raccomandazioni e la funzione di ognuna di esse se applicate.

Jeff scopre che il [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni su minacce e segnalazioni di violazioni da terzi. Se Jeff inserisce il contatto per la sicurezza di Contoso per la sottoscrizione di Azure, Microsoft contatta Contoso se MSRC rileva che i dati dei clienti di Contoso sono stati violati da altri utenti non autorizzati in modo illegale. Vediamo come Jeff applica la raccomandazione **Provide security contact details** (Inserisci i dettagli dei contatti per la sicurezza) (una raccomandazione con livello di gravità medio nell'elenco delle raccomandazioni sopra indicato).

1. Jeff seleziona **Provide security contact details** (Inserisci i dettagli dei contatti per la sicurezza) nel pannello **Raccomandazioni**, che consente di aprire il pannello **Provide security contact details** (Inserisci i dettagli dei contatti per la sicurezza).
2. Jeff seleziona la sottoscrizione di Azure per cui specifica le informazioni di contatto. Viene visualizzato un altro pannello **Specificare i dettagli dei contatti di sicurezza** .
   ![Dettagli del contatto per la sicurezza][6]
3. Nel secondo pannello **Provide security contact details** (Inserisci i dettagli dei contatti per la sicurezza), Jeff inserisce:

  - gli indirizzi di posta elettronica dei contatti per la sicurezza separati da virgole (non c'è un limite al numero di indirizzi di posta elettronica che è possibile inserire)
  - un numero di telefono del contatto per la sicurezza

4. Per ricevere messaggi di posta elettronica relativi agli avvisi di elevata gravità, Jeff inoltre attiva l'opzione **Send me emails about alerts**(Invia messaggi di posta elettronica relativi agli avvisi).
5. Jeff seleziona **OK** per applicare le informazioni di contatto di sicurezza alla sottoscrizione di Contoso.

Infine, Jeff esamina le raccomandazioni con priorità bassa **Remediate OS vulnerabilities** (Correggi le vulnerabilità del sistema operativo) e decide che questa raccomandazione non è applicabile. Vuole ignorare la raccomandazione. Jeff seleziona i tre puntini che appaiono a destra, quindi seleziona **Ignora**.
   ![Ignorare la raccomandazione][7]

## <a name="conclusion"></a>Conclusione
Il monitoraggio delle raccomandazioni nel Centro sicurezza PC può contribuire a eliminare le vulnerabilità di sicurezza prima che si verifichi un attacco. È possibile evitare problemi di sicurezza applicando e gestendo le protezioni con i criteri di protezione nel Centro sicurezza.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png



<!--HONumber=Jan17_HO2-->


