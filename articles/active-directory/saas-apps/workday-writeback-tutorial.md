---
title: 'Esercitazione: Configurare il writeback di Workday in Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare il writeback degli attributi da Azure AD a Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: c65fddcc90b25f70759fb038a72dad0facfa99a9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359732"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Esercitazione: Configurare il writeback degli attributi da Azure AD a Workday
Questa esercitazione illustra la procedura per eseguire il writeback degli attributi da Azure AD a Workday. L'app di provisioning Writeback di Workday supporta l'assegnazione di valori agli attributi seguenti di Workday:
* Indirizzo di posta elettronica aziendale 
* Nome utente di Workday
* Numero di telefono aziendale fisso (comprensivo di codice paese, prefisso, numero e interno)
* Flag primario del numero di telefono fisso aziendale
* Numero di telefono cellulare aziendale (comprensivo di codice paese, prefisso, numero)
* Flag primario del cellulare aziendale

## <a name="overview"></a>Panoramica

Dopo aver configurato l'integrazione del provisioning in ingresso usando l'app di provisioning da [Workday ad AD locale](workday-inbound-tutorial.md) o l'app di provisioning da [Workday ad Azure AD](workday-inbound-cloud-only-tutorial.md), facoltativamente è possibile configurare l'app Writeback di Workday per scrivere informazioni di contatto come l'indirizzo di posta elettronica aziendale e il numero di telefono in Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti Writeback di Workday è idealmente appropriata per:

* Le organizzazioni che usano Microsoft 365 e vogliono eseguire il writeback degli attributi autorevoli gestiti dall'IT (ad esempio indirizzo di posta elettronica, nome utente e numero di telefono) in Workday

## <a name="configure-integration-system-user-in-workday"></a>Configurare un utente del sistema di integrazione in Workday

Vedere la sezione [Configurare un utente del sistema di integrazione](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) per informazioni su come creare un account utente del sistema di integrazione di Workday con le autorizzazioni per il recupero dei dati dei lavoratori. 

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

1. Nella scheda Provisioning, in **Mapping**, fare clic sul nome del mapping.

2. Nel campo **Ambito dell'oggetto di origine** è possibile scegliere di filtrare i set di utenti di Azure Active Directory che dovranno far parte del writeback. L'ambito predefinito è "tutti gli utenti in Azure AD".

3. Nella sezione **Mapping degli attributi**, aggiornare l'ID corrispondente per indicare l'attributo in Azure Active Directory in cui è memorizzato l'ID del ruolo di lavoro Workday o l'ID del dipendente. Un metodo comune per garantire la corrispondenza è sincronizzare l'ID lavoratore o l'ID dipendente di Workday in extensionAttribute1-15 in Azure AD e quindi usare questo attributo in Azure AD per associare gli utenti in Workday.

4. In genere si esegue il mapping dell'attributo di Azure AD *userPrincipalName* nell'attributo *UserID* di Workday e il mapping dell'attributo *mail* di Azure AD nell'attributo *EmailAddress* di Workday. 

     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Usare le indicazioni riportate di seguito per eseguire il mapping dei valori degli attributi dei numeri di telefono da Azure AD a Workday. 

     | Attributo del numero di telefono di Workday | Valore previsto | Indicazioni sul mapping |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | Mapping di costanti o espressioni il cui output è un valore di stringa "true" o "false". |
     | WorkphoneLandlineCountryCodeName | [Codice paese ISO 3166-1 di tre lettere](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapping di costanti o espressioni il cui output è un codice paese di tre lettere. |
     | WorkphoneLandlineCountryCodeNumber | [Prefisso telefonico internazionale](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapping di costanti o espressioni il cui output è un codice paese valido (senza il segno +). |
     | WorkphoneLandlineNumber | Numero di telefono completo che include il prefisso | Mapping all'attributo *telephoneNumber*. Usare regex per rimuovere spazi, parentesi quadre e codice paese. Vedi l'esempio seguente. |
     | WorkphoneLandlineExtension | Numero di interno | Se *telephoneNumber* contiene l'interno, usare regex per estrarre il valore. |
     | WorkphoneMobileIsPrimary | true/false | Mapping di costanti o espressioni il cui output è un valore di stringa "true" o "false" |
     | WorkphoneMobileCountryCodeName | [Codice paese ISO 3166-1 di tre lettere](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapping di costanti o espressioni il cui output è un codice paese di tre lettere. |
     | WorkphoneMobileCountryCodeNumber | [Prefisso telefonico internazionale](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapping di costanti o espressioni il cui output è un codice paese valido (senza il segno +). |
     | WorkphoneMobileNumber | Numero di telefono completo che include il prefisso | Mapping all'attributo *mobile*. Usare regex per rimuovere spazi, parentesi quadre e codice paese. Vedi l'esempio seguente. |

     > [!NOTE]
     > Quando si richiama il servizio Web Change_Work_Contact di Workday, Azure AD invia i valori costanti seguenti: <br>
     > * **Communication_Usage_Type_ID** è impostato sulla stringa costante "WORK" <br>
     > * **Phone_Device_Type_ID** è impostato sulla stringa costante "Mobile" per i numeri di telefono cellulare e "Landline" per i numeri di telefono fissi. <br>
     > 
     > Se il tenant di Workday usa valori di Type_ID diversi, si verificano errori di writeback. Per evitare tali errori, è possibile usare l'attività **Maintain Reference IDs** (Mantieni ID di riferimento) di Workday e aggiornare i Type_ID in base ai valori usati da Azure AD. <br>
     >  

     **Espressioni regex di riferimento - Esempio 1**

     Usare l'espressione regolare seguente se il numero di telefono in Azure AD è impostato nel formato richiesto per la reimpostazione della password self-service. <br>
     Esempio: se il valore del numero di telefono è + 1 1112223333, l'espressione regex restituirà 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Espressioni regex di riferimento - Esempio 2**

     Usare l'espressione regolare seguente se il numero di telefono in Azure AD è impostato nel formato (XXX) XXX-XXXX. <br>
     Esempio: se il valore del numero di telefono è (111) 222-3333, l'espressione regolare restituirà 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

## <a name="enable-and-launch-user-provisioning"></a>Abilitare e avviare il provisioning utenti

Dopo aver completato le configurazioni dell'app di provisioning Workday, è possibile attivare il servizio di provisioning nel portale di Azure.

> [!TIP]
> Per impostazione predefinita quando si attiva il servizio di provisioning, verranno avviate le operazioni di provisioning per tutti gli utenti nell'ambito. Se sono presenti errori di mapping o problemi di dati in Workday, il processo di provisioning potrebbe non riuscire e passare allo stato di quarantena. Come procedura consigliata per evitare questo problema è consigliabile configurare il filtro **Source Object Scope** (Ambito dell'oggetto di origine) e il test di mapping degli attributi con alcuni utenti test prima di avviare la sincronizzazione completa per tutti gli utenti. Dopo avere verificato che i mapping funzionino e che restituiscano i risultati desiderati è possibile rimuovere il filtro o espanderlo gradualmente in modo da includere altri utenti.

1. Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

1. Nell'elenco a discesa **Ambito** selezionare **Sincronizza tutti gli utenti e i gruppi**. Con questa opzione l'app Writeback eseguirà il writeback degli attributi mappati di tutti gli utenti da Azure AD a Workday, in base alle regole di definizione dell'ambito specificate in **Mapping** -> **Ambito dell'oggetto di origine**. 

   > [!div class="mx-imgBorder"]
   > ![Selezione dell'ambito del writeback](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > L'app di provisioning Writeback di Workday non supporta l'opzione **Sincronizza solo utenti e gruppi assegnati**.
 

2. Fare clic su **Salva**.

3. Questa operazione avvierà la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti presenti nella directory di origine. È possibile controllare l'indicatore di stato per monitorare l'avanzamento del ciclo di sincronizzazione. 

4. In qualsiasi momento è possibile controllare la scheda **Log di provisioning** nel portale di Azure per vedere quali azioni sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono importati dall'origine ed esportati nell'applicazione di destinazione.  

5. Al termine della sincronizzazione iniziale, verrà scritto un report di riepilogo nella scheda **Provisioning**, come illustrato di seguito.

     > [!div class="mx-imgBorder"]
     > ![Indicatore di stato del provisioning](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

* L'app Writeback usa un valore predefinito per i parametri **Communication_Usage_Type_ID** e **Phone_Device_Type_ID**. Se il tenant di Workday usa un valore diverso per questi attributi, l'operazione di writeback avrà esito negativo. Una soluzione alternativa consigliata consiste nell'aggiornare i valori di Type_ID in Workday. 
* Se l'app Writeback è configurata per l'aggiornamento dei numeri di telefono secondari, non sostituisce il numero di telefono secondario esistente in Workday, ma ne aggiunge un altro al record del lavoratore. Non sono disponibili soluzioni alternative per questo comportamento. 


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni su come esportare e importare le configurazioni del provisioning](../app-provisioning/export-import-provisioning-configuration.md)

