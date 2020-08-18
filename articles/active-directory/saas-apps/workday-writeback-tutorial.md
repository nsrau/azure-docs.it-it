---
title: 'Esercitazione: configurare il writeback della giornata lavorativa in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il writeback degli attributi da Azure AD a giornata lavorativa
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 324d3a21e1694d243f03beca28ac8376bedffa4d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88526831"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Esercitazione: configurare il writeback degli attributi da Azure AD a giornata lavorativa
L'obiettivo di questa esercitazione è mostrare i passaggi da eseguire per gli attributi di writeback da Azure AD a giornata lavorativa. L'app di provisioning writeback dei giorni lavorativi supporta l'assegnazione di valori agli attributi di giornata lavorativi seguenti:
* Indirizzo di posta elettronica aziendale 
* Nome utente giornata lavorativa
* Numero di telefono fisso del lavoro (incluso codice paese, prefisso, numero ed estensione)
* Contrassegno primario numero di telefono fisso lavoro
* Numero di lavoro mobile (incluso il codice paese, il codice di area, numero)
* Contrassegno primario mobile di lavoro

## <a name="overview"></a>Panoramica

Dopo aver configurato l'integrazione del provisioning in ingresso usando la [giornata di lavoro per](workday-inbound-tutorial.md) l'app di provisioning di ad locale o la [giornata lavorativa per Azure ad app di](workday-inbound-cloud-only-tutorial.md) provisioning, è possibile configurare facoltativamente l'app writeback dei giorni lavorativi per scrivere informazioni di contatto, ad esempio posta elettronica e numero di telefono per la giornata lavorativa 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti writeback dei giorni lavorativi è ideale per:

* Organizzazioni che usano Office 365 che desiderano eseguire il writeback degli attributi autorevoli gestiti dall'IT (ad esempio indirizzo di posta elettronica, nome utente e numero di telefono) alla giornata lavorativa

## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Vedere la sezione [configurare l'utente del sistema di integrazione](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) per la creazione di un account utente del sistema di integrazione lavorativa con le autorizzazioni per recuperare i dati di lavoro. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurazione del writeback degli attributi di Azure AD in Workday

Seguire queste istruzioni per configurare il writeback degli indirizzi di posta elettronica e dei nomi utente degli utenti da Azure Active Directory in Workday.

* [Aggiungere l'app del connettore Writeback e creare la connessione a Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurare i mapping degli attributi di writeback](#part-2-configure-writeback-attribute-mappings)
* [Abilitare e avviare il provisioning utenti](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Aggiungere l'app del connettore Writeback e creare la connessione a Workday

**Per configurare il connettore di Workday Writeback:**

1. Passare a <https://portal.azure.com>.

2. Nel portale di Azure cercare e selezionare **Azure Active Directory**.

3. Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4. Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5. Cercare **Workday Writeback** (Writeback Workday) e aggiungere tale applicazione dalla raccolta.

6. Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**.

7. Modificare **Modalità** di **provisioning** su **Automatico**.

8. Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Il risultato sarà simile al seguente: *username\@contoso4*

   * **Password dell'amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`, dove *contoso4* è sostituito dal nome del tenant corretto e *wd3-impl* è sostituito dalla stringa di ambiente corretta (se necessario).

   * **Messaggio di posta elettronica di notifica:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo per inviare una notifica di posta elettronica in caso di errore.

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi di writeback

In questa sezione si configurerà il flusso degli attributi writeback da Azure AD a Workday. 

1. Nella scheda provisioning, in **mapping**, fare clic sul nome del mapping.

2. Nel campo **ambito dell'oggetto di origine** è possibile filtrare facoltativamente i set di utenti in Azure Active Directory devono far parte del writeback. L'ambito predefinito è "tutti gli utenti in Azure AD".

3. Nella sezione **Mapping degli attributi**, aggiornare l'ID corrispondente per indicare l'attributo in Azure Active Directory in cui è memorizzato l'ID del ruolo di lavoro Workday o l'ID del dipendente. Un metodo comune per garantire la corrispondenza è sincronizzare l'ID lavoratore o l'ID dipendente di Workday in extensionAttribute1-15 in Azure AD e quindi usare questo attributo in Azure AD per associare gli utenti in Workday.

4. In genere si esegue il mapping dell'attributo di Azure AD *userPrincipalName* nell'attributo *UserID* di Workday e il mapping dell'attributo *mail* di Azure AD nell'attributo *EmailAddress* di Workday. 

     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Usare le indicazioni condivise di seguito per eseguire il mapping dei valori degli attributi dei numeri di telefono da Azure AD a giornata lavorativa 

     | Attributo telefono giorni lavorativi | Valore previsto | Linee guida per il mapping |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | Costante o mapping di espressioni il cui output è "true" o "false" valore stringa. |
     | WorkphoneLandlineCountryCodeName | [Codice paese ISO 3166-1 di tre lettere](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Costante o mapping di espressioni il cui output è un codice paese di tre lettere. |
     | WorkphoneLandlineCountryCodeNumber | [Codice internazionale che chiama il paese](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Costante o mapping di espressioni il cui output è un codice paese valido (senza il segno +). |
     | WorkphoneLandlineNumber | Numero di telefono completo che include il prefisso | Eseguire il mapping all'attributo *telephoneNumber* . Usare Regex per rimuovere spazi, parentesi quadre e codice paese. Vedi l'esempio seguente. |
     | WorkphoneLandlineExtension | Numero di estensione | Se *telephoneNumber* contiene l'estensione, usare Regex per estrarre il valore. |
     | WorkphoneMobileIsPrimary | true/false | Mapping costante o mapping di espressioni il cui output è "true" o "false" valore stringa |
     | WorkphoneMobileCountryCodeName | [Codice paese ISO 3166-1 di tre lettere](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Costante o mapping di espressioni il cui output è un codice paese di tre lettere. |
     | WorkphoneMobileCountryCodeNumber | [Codice internazionale che chiama il paese](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Costante o mapping di espressioni il cui output è un codice paese valido (senza il segno +). |
     | WorkphoneMobileNumber | Numero di telefono completo che include il prefisso | Eseguire il mapping a un attributo per *dispositivi mobili* . Usare Regex per rimuovere spazi, parentesi quadre e codice paese. Vedi l'esempio seguente. |

     > [!NOTE]
     > Quando si richiama il servizio Web Change_Work_Contact giorni lavorativi, Azure AD invia i valori costanti seguenti: <br>
     > * **Communication_Usage_Type_ID** è impostato sulla stringa costante "Work" <br>
     > * **Phone_Device_Type_ID** è impostato sulla stringa costante "mobile" per i numeri di telefono cellulare e "rete fissa" per i numeri di telefono fissi. <br>
     > 
     > Se il tenant della giornata lavorativa USA Type_IDs diversi, si verificheranno errori di writeback. Per evitare tali errori, è possibile utilizzare l'attività **manutenzione degli ID di riferimento** e aggiornare le Type_IDs in modo che corrispondano ai valori utilizzati da Azure ad. <br>
     >  

     **Espressioni Regex di riferimento-esempio 1**

     Utilizzare l'espressione regolare seguente se il numero di telefono in Azure AD viene impostato utilizzando il formato necessario per la reimpostazione della password self-service (SSPR). <br>
     Esempio: se il valore del numero di telefono è + 1 1112223333->, l'espressione Regex verrà restituita 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Espressioni Regex di riferimento-esempio 2**

     Utilizzare l'espressione regolare seguente se il numero di telefono in Azure AD viene impostato utilizzando il formato (XXX) XXX-XXXX. <br>
     Esempio: se il valore del numero di telefono è (111) 222-3333->, l'espressione Regex verrà restituita 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning Workday, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nella directory di origine. È possibile controllare l'indicatore di stato per tenere traccia dello stato di avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti gli eventi di sincronizzazione singoli eseguiti dal servizio di provisioning, ad esempio gli utenti importati dall'origine e esportati nell'applicazione di destinazione.  

5. Una volta completata la sincronizzazione iniziale, viene scritto un report riepilogativo nella scheda **provisioning** , come illustrato di seguito.

     > [!div class="mx-imgBorder"]
     > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni di provisioning](../app-provisioning/export-import-provisioning-configuration.md)

