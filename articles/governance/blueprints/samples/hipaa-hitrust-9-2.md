---
title: Panoramica dell'esempio di progetto HIPAA HITRUST 9.2
description: Panoramica dell'esempio di progetto HIPAA HITRUST 9.2. Questo esempio di progetto consente ai clienti di valutare specifici controlli HIPAA HITRUST 9.2.
ms.date: 09/04/2020
ms.topic: sample
ms.openlocfilehash: 4df6f05019976b3de1172cc5127c27ac00fe3c44
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493229"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Esempio di progetto HIPAA HITRUST 9.2

L'esempio di progetto HIPAA HITRUST 9.2 fornisce misure di tutela per la governance tramite [Criteri di Azure](../../policy/overview.md) che consentono di valutare specifici controlli HIPAA HITRUST 9.2. Il progetto aiuta i clienti a distribuire un set di base di criteri per qualsiasi architettura distribuita in Azure che deve implementare i controlli HIPAA HITRUST 9.2.

## <a name="control-mapping"></a>Mapping dei controlli

Il [mapping dei controlli di Criteri di Azure](../../policy/samples/hipaa-hitrust-9-2.md) fornisce informazioni dettagliate sulle definizioni dei criteri incluse in questo progetto e sul relativo mapping ai **domini di conformità** e ai **controlli** di HIPAA HITRUST 9.2. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire l'esempio di progetto HIPAA HITRUST 9.2 di Azure Blueprints, è necessario seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **HIPAA HITRUST** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto HIPAA HITRUST 9.2.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non allineata ai controlli HIPAA HITRUST 9.2.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto HIPAA HITRUST 9.2". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

### <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

### <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto |Nome parametro |Descrizione |
|---|---|---|
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |L'accesso tramite endpoint con connessione Internet deve essere limitato |Abilita o disabilita il monitoraggio delle regole in ingresso dei gruppi di sicurezza di rete eccessivamente permissive |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Account: stato account guest |Specifica se l'account Guest locale è disabilitato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali |Abilita o disabilita il monitoraggio dell'inserimento delle applicazioni nell'elenco elementi consentiti nel Centro sicurezza di Azure |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Consenti connessioni simultanee a Internet o a un dominio Windows |Specifica se impedire ai computer di connettersi contemporaneamente sia a una rete di dominio che a una rete non di dominio. Un valore di 0 consente le connessioni simultanee e un valore di 1 le blocca. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |L'app per le API deve essere accessibile solo tramite HTTPS V2 |Abilita o disabilita il monitoraggio dell'uso di HTTPS V2 nell'app per le API |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Nomi delle applicazioni (supporta caratteri jolly) |Elenco delimitato da punto e virgola con i nomi delle applicazioni che devono essere installate. Ad esempio: 'Microsoft SQL Server 2014 (64-bit); Microsoft Visual Studio Code' o 'Microsoft SQL Server 2014*' (per trovare una corrispondenza con qualsiasi applicazione che inizi con 'Microsoft SQL Server 2014') |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Controlla Chiusura di processi |Specifica se vengono generati gli eventi di controllo quando un processo è terminato. Consigliato per il monitoraggio della terminazione di processi critici. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Controlla l'accesso di rete senza restrizioni agli account di archiviazione |Abilita o disabilita il monitoraggio dell'accesso di rete all'account di archiviazione |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Controllo: arresto del sistema immediato se non è possibile registrare i controlli di sicurezza |Controlla se il sistema verrà arrestato quando non è possibile registrare gli eventi di sicurezza. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Identificazioni personali dei certificati |Elenco delimitato da punto e virgola con le identificazioni personali dei certificati che devono essere presenti nell'archivio certificati radice trusted (Cert: LocalMachine Root). Ad esempio: IDENTIFICAZIONE_PERSONALE1;IDENTIFICAZIONE_PERSONALE2;IDENTIFICAZIONE_PERSONALE3 |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile abilitare i log di diagnostica negli account Batch |Abilita o disabilita il monitoraggio dei log di diagnostica negli account Batch |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile abilitare i log di diagnostica in Hub eventi |Abilita o disabilita il monitoraggio dei log di diagnostica negli account Hub eventi |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile abilitare i log di diagnostica nei servizi di ricerca |Abilita o disabilita il monitoraggio dei log di diagnostica nel servizio Ricerca di Azure |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile abilitare i log di diagnostica nei set di scalabilità di macchine virtuali |Abilita o disabilita il monitoraggio dei log di diagnostica in Service Fabric |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |La crittografia del disco deve essere applicata nelle macchine virtuali |Abilita o disabilita il monitoraggio della crittografia dei dischi delle macchine virtuali |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Abilita gli accessi guest non sicuri |Specifica se il client SMB consente gli accessi guest non sicuri a un server SMB. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT |Abilita o disabilita il monitoraggio dell'accesso JIT alla rete |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile chiudere le porte di gestione nelle macchine virtuali |Abilita o disabilita il monitoraggio delle porte di gestione aperte nelle macchine virtuali |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione |Abilita o disabilita il monitoraggio di MFA per gli account con autorizzazioni di scrittura nella sottoscrizione |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione |Abilita o disabilita il monitoraggio di MFA per gli account con autorizzazioni di proprietario nella sottoscrizione |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Accesso di rete: percorsi del Registro di sistema ai quali è possibile accedere in modo remoto |Specifica i percorsi del Registro di sistema che saranno accessibili tramite la rete, indipendentemente dagli utenti o i gruppi elencati nell'elenco di controllo di accesso (ACL) della chiave del Registro di sistema `winreg`. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Accesso alla rete: percorsi e sottopercorsi del Registro di sistema ai quali è possibile accedere in modalità remota |Specifica i percorsi e i sottopercorsi del Registro di sistema che saranno accessibili tramite la rete, indipendentemente dagli utenti o i gruppi elencati nell'elenco di controllo di accesso (ACL) della chiave del Registro di sistema `winreg`. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Accesso di rete: condivisioni alle quali è possibile accedere in modo anonimo |Specifica le condivisioni di rete a cui possono accedere gli utenti anonimi. La configurazione predefinita per questa impostazione dei criteri ha un effetto ridotto perché tutti gli utenti devono essere autenticati prima di poter accedere alle risorse condivise nel server. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Console di ripristino di emergenza: consenti la copia di dischi floppy e l'accesso a tutte le unità e cartelle |Specifica se rendere disponibile il comando SET della console di ripristino di emergenza, che consente di impostare le variabili di ambiente della console di ripristino di emergenza. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Il debug remoto deve essere disattivato per l'app per le API |Abilita o disabilita il monitoraggio del debug remoto per l'app per le API |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Il debug remoto deve essere disattivato per l'applicazione Web |Abilita o disabilita il monitoraggio del debug remoto per l'app Web |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Conservazione necessaria (in giorni) per i log negli account Batch |Periodo di conservazione necessario dei log di diagnostica in giorni |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Conservazione necessaria (in giorni) dei log nel servizio Ricerca di Azure |Periodo di conservazione necessario dei log di diagnostica in giorni |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Conservazione necessaria (in giorni) dei log negli account Hub eventi |Periodo di conservazione necessario dei log di diagnostica in giorni |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Nome del gruppo di risorse per l'account di archiviazione (deve esistere) per distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete |Il gruppo di risorse in cui verrà creato l'account di archiviazione. Questo gruppo di risorse deve già esistere. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes |Abilita o disabilita il monitoraggio dei servizi Kubernetes senza controllo degli accessi in base al ruolo abilitato |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata |Abilita o disabilita il monitoraggio di Transparent Data Encryption con il supporto della chiave personale che offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata |Abilita o disabilita il monitoraggio di Transparent Data Encryption con il supporto della chiave personale che offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Prefisso dell'account di archiviazione per l'account di archiviazione a livello di area per distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete |Questo prefisso verrà combinato con la posizione del gruppo di sicurezza di rete per formare il nome dell'account di archiviazione creato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati |Abilita o disabilita la segnalazione degli aggiornamenti del sistema per i set di scalabilità di macchine virtuali |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati |Abilita o disabilita la segnalazione degli aggiornamenti del sistema per i set di scalabilità di macchine virtuali |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Disattiva risoluzione dei nomi multicast |Specifica se LLMNR, un protocollo secondario di risoluzione dei nomi che trasmette usando il multicasting tramite un collegamento subnet locale in una singola subnet, è abilitato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager |Abilita o disabilita il monitoraggio delle macchine virtuali di calcolo della versione classica |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte |Abilita o disabilita il monitoraggio delle vulnerabilità del sistema operativo per i set di scalabilità di macchine virtuali |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità |Abilita o disabilita il rilevamento delle vulnerabilità delle macchine virtuali tramite una soluzione di valutazione della vulnerabilità |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL |Controlla le istanze gestite di SQL in cui non sono abilitate analisi di valutazione della vulnerabilità ricorrenti. La valutazione della vulnerabilità consente di individuare, monitorare e risolvere le potenziali vulnerabilità del database. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): applica regole firewall locali |Specifica se agli amministratori locali è consentito creare regole del firewall locali che si applicano insieme alle regole del firewall configurate da Criteri di gruppo per il profilo di dominio. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): comportamento per le connessioni in uscita |Specifica il comportamento delle connessioni in uscita per il profilo di dominio che non corrispondono a una regola del firewall in uscita. Il valore predefinito 0 indica che le connessioni sono consentite, il valore 1 indica che le connessioni sono bloccate. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): comportamento per le connessioni in uscita |Specifica il comportamento delle connessioni in uscita per il profilo di dominio che non corrispondono a una regola del firewall in uscita. Il valore predefinito 0 indica che le connessioni sono consentite, il valore 1 indica che le connessioni sono bloccate. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): Visualizzazione notifiche |Specifica se Windows Firewall con protezione avanzata visualizza le notifiche all'utente quando un programma è bloccato e non può ricevere connessioni in entrata per il profilo di dominio. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): usa impostazioni del profilo |Specifica se Windows Firewall con protezione avanzata usa le impostazioni del profilo di dominio per filtrare il traffico di rete. Se si seleziona No, Windows Firewall con protezione avanzata non userà le regole del firewall o le regole di sicurezza di connessione per questo profilo. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): applica regole di sicurezza di connessione locali |Specifica se agli amministratori locali è consentito creare regole di sicurezza di connessione che si applicano insieme alle regole di sicurezza di connessione configurate da Criteri di gruppo per il profilo privato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): applica regole firewall locali |Specifica se agli amministratori locali è consentito creare regole del firewall locali che si applicano insieme alle regole del firewall configurate da Criteri di gruppo per il profilo privato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): comportamento per le connessioni in uscita |Specifica il comportamento delle connessioni in uscita per il profilo privato che non corrispondono a una regola del firewall in uscita. Il valore predefinito 0 indica che le connessioni sono consentite, il valore 1 indica che le connessioni sono bloccate. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): Visualizzazione notifiche |Specifica se Windows Firewall con protezione avanzata visualizza le notifiche all'utente quando un programma è bloccato e non può ricevere connessioni in entrata per il profilo privato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): usa impostazioni del profilo |Specifica se Windows Firewall con protezione avanzata usa le impostazioni del profilo privato per filtrare il traffico di rete. Se si seleziona No, Windows Firewall con protezione avanzata non userà le regole del firewall o le regole di sicurezza di connessione per questo profilo. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): applica regole di sicurezza di connessione locali |Specifica se agli amministratori locali è consentito creare regole di sicurezza di connessione che si applicano insieme alle regole di sicurezza di connessione configurate da Criteri di gruppo per il profilo pubblico. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): applica regole firewall locali |Specifica se agli amministratori locali è consentito creare regole del firewall locali che si applicano insieme alle regole del firewall configurate da Criteri di gruppo per il profilo pubblico. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): comportamento per le connessioni in uscita |Specifica il comportamento delle connessioni in uscita per il profilo pubblico che non corrispondono a una regola del firewall in uscita. Il valore predefinito 0 indica che le connessioni sono consentite, il valore 1 indica che le connessioni sono bloccate. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): Visualizzazione notifiche |Specifica se Windows Firewall con protezione avanzata visualizza le notifiche all'utente quando un programma è bloccato e non può ricevere connessioni in entrata per il profilo pubblico. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): usa impostazioni del profilo |Specifica se Windows Firewall con protezione avanzata usa le impostazioni del profilo pubblico per filtrare il traffico di rete. Se si seleziona No, Windows Firewall con protezione avanzata non userà le regole del firewall o le regole di sicurezza di connessione per questo profilo. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (dominio): consenti risposta unicast |Specifica se Windows Firewall con protezione avanzata consente al computer locale di ricevere risposte unicast ai messaggi in uscita multicast o broadcast per il profilo di dominio. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (privato): consenti risposta unicast |Specifica se Windows Firewall con protezione avanzata consente al computer locale di ricevere risposte unicast ai messaggi in uscita multicast o broadcast per il profilo privato. |
|Verifica controlli HITRUST/HIPAA e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo |Windows Firewall (pubblico): consenti risposta unicast |Specifica se Windows Firewall con protezione avanzata consente al computer locale di ricevere risposte unicast ai messaggi in uscita multicast o broadcast per il profilo pubblico. |

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
