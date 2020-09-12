---
title: Panoramica della sicurezza
description: Informazioni di sicurezza sui server abilitati per Azure Arc (anteprima).
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 17641fab9933d9d6a60c2b21912f755acc01a6dd
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447858"
---
# <a name="azure-arc-for-servers-preview-security-overview"></a>Panoramica della sicurezza di Azure Arc for Servers (anteprima)

Questo articolo descrive la configurazione della sicurezza e le considerazioni da valutare prima di distribuire i server abilitati per Azure Arc nell'azienda.

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

Ogni server abilitato per Azure Arc ha un'identità gestita come parte di un gruppo di risorse all'interno di una sottoscrizione di Azure che rappresenta il server in esecuzione in locale o in un altro ambiente cloud. L'accesso a questa risorsa è controllato dal [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)standard. Dalla pagina [**controllo di accesso (IAM)**](../../role-based-access-control/role-assignments-portal.md#access-control-iam) nella portale di Azure è possibile verificare chi ha accesso al server abilitato per Azure Arc.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Controllo di accesso del server abilitato per Azure Arc" border="false" lightbox="./media/security-overview/access-control-page.png":::

Gli utenti e le applicazioni a cui è stato concesso l'accesso al ruolo di amministratore o [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) alla risorsa possono apportare modifiche alla risorsa, inclusa la distribuzione o l'eliminazione di [estensioni](manage-vm-extensions.md) nel computer. Le estensioni possono includere script arbitrari eseguiti in un contesto con privilegi, quindi considerare qualsiasi collaboratore nella risorsa di Azure come amministratore indiretto del server non Azure.

Il ruolo di **onboarding del computer connesso di Azure** è disponibile per il caricamento su larga scala ed è in grado di leggere o creare nuovi server abilitati per Arc in Azure. Non può essere usato per eliminare i server già registrati o per gestire le estensioni. Come procedura consigliata, è consigliabile assegnare questo ruolo all'entità servizio Azure Active Directory (Azure AD) utilizzata per l'onboarding dei computer su larga scala.

Gli utenti come membri del ruolo di **amministratore delle risorse del computer connesso ad Azure** possono leggere, modificare, onboarding ed eliminare un computer. Questo ruolo è progettato per supportare la gestione dei server abilitati per Arc, ma non altre risorse nel gruppo di risorse o nella sottoscrizione.

## <a name="agent-security-and-permissions"></a>Sicurezza e autorizzazioni dell'agente

Per gestire l'agente del computer connesso di Azure (azcmagent), in Windows l'account utente deve essere un membro del gruppo Administrators locale e in Linux è necessario disporre delle autorizzazioni di accesso alla radice.

L'agente del computer connesso di Azure è costituito da tre servizi che vengono eseguiti nel computer.

* Il servizio himds (Hybrid instance Metadata Service) è responsabile di tutte le funzionalità di base di Arc. Ciò include l'invio di heartbeat ad Azure, l'esposizione di un servizio metadati dell'istanza locale per altre app per informazioni sull'ID della risorsa di Azure del computer e il recupero dei token di Azure AD per l'autenticazione ad altri servizi di Azure. Questo servizio viene eseguito come account del servizio virtuale senza privilegi in Windows e come utente **himds** in Linux.

* Il servizio di configurazione Guest (GCService) è responsabile della valutazione dei criteri di Azure nel computer.

* Il servizio estensione configurazione Guest (ExtensionService) è responsabile dell'installazione, dell'aggiornamento e dell'eliminazione delle estensioni (agenti, script o altro software) nel computer.

I servizi di configurazione e di estensione Guest vengono eseguiti come sistema locale in Windows e come root in Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Uso di un'identità gestita con server abilitati per Arc

Per impostazione predefinita, l'identità assegnata dal sistema Azure Active Directory usata da Arc può essere usata solo per aggiornare lo stato del server abilitato per l'arco in Azure. Ad esempio, l' *ultimo* stato heartbeat visualizzato. Facoltativamente, è possibile assegnare ruoli aggiuntivi all'identità se un'applicazione nel server usa l'identità assegnata dal sistema per accedere ad altri servizi di Azure.

Mentre è possibile accedere al servizio metadati dell'istanza ibrida da qualsiasi applicazione in esecuzione nel computer, solo le applicazioni autorizzate possono richiedere un token di Azure AD per l'identità assegnata dal sistema. Al primo tentativo di accesso all'URI del token, il servizio genererà un BLOB crittografico generato in modo casuale in un percorso nel file system che possono essere letti solo dai chiamanti attendibili. Il chiamante deve quindi leggere il file (dimostrando che ha l'autorizzazione appropriata) e ripetere la richiesta con il contenuto del file nell'intestazione dell'autorizzazione per recuperare correttamente un token di Azure AD.

* In Windows, il chiamante deve essere un membro del gruppo **Administrators** locale o del gruppo di **applicazioni di estensione dell'agente ibrido** per la lettura del BLOB.

* In Linux, il chiamante deve essere un membro del gruppo **himds** per la lettura del BLOB.

## <a name="using-disk-encryption"></a>Uso della crittografia del disco

L'agente del computer connesso di Azure usa l'autenticazione con chiave pubblica per comunicare con il servizio di Azure. Dopo l'onboarding di un server in Azure Arc, una chiave privata viene salvata sul disco e usata ogni volta che l'agente comunica con Azure. Se rubato, la chiave privata può essere utilizzata in un altro server per comunicare con il servizio e fungere da server originale. Questo include l'accesso all'identità assegnata al sistema e alle risorse a cui ha accesso l'identità. Il file di chiave privata è protetto in modo da consentire l'accesso solo all'account **himds** per leggerlo. Per evitare attacchi offline, si consiglia vivamente di utilizzare la crittografia completa del disco, ad esempio BitLocker, dm-crypt e così via, nel volume del sistema operativo del server.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare i server abilitati per Arc (anteprima) tra più computer ibridi, vedere [Panoramica dell'agente Connected Machine](agent-overview.md) per informazioni su requisiti, i dettagli tecnici sull'agente e metodi di distribuzione.
