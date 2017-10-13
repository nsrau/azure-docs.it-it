---
title: 'Lezione supplementare dell''esercitazione su Azure Analysis Services: sicurezza dinamica | Microsoft Docs'
description: Descrive come usare la sicurezza dinamica tramite filtri di riga nell'esercitazione su Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: b258c18fde15014192e8f604a4e8b3842c3e52c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---dynamic-security"></a>Lezione supplementare: Sicurezza dinamica

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

In questa lezione supplementare si creerà un ruolo aggiuntivo che implementa la sicurezza dinamica. La sicurezza dinamica offre un meccanismo di protezione a livello di riga basato sul nome utente o sull'ID di accesso dell'utente connesso. 
  
Per implementare la sicurezza dinamica, è necessario aggiungere al modello una tabella con i nomi degli utenti che possono connettersi al modello ed esplorare dati e oggetti del modello. Il modello creato con questa esercitazione è nel contesto di Adventure Works. Per completare questa lezione, tuttavia, è necessario aggiungere una tabella contenente gli utenti del proprio dominio. Non occorrono le password per i nomi utente aggiunti. Per creare una tabella EmployeeSecurity con un piccolo campione di utenti del proprio dominio, usare la funzionalità Incolla e incollare i dati dei dipendenti da un foglio di calcolo di Excel. In uno scenario reale, la tabella contenente i nomi utente sarebbe di solito una tabella da un database effettivo usato come origine dati, ad esempio una tabella DimEmployee reale.  
  
Per implementare la sicurezza dinamica, verranno usate due funzioni DAX: [USERNAME](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) e [LOOKUPVALUE](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Queste funzioni, applicate in una formula di filtro di riga, vengono definite in un nuovo ruolo. Usando la funzione LOOKUPVALUE, la formula specifica un valore dalla tabella EmployeeSecurity e quindi passa il valore alla funzione USERNAME, che specifica che il nome dell'utente connesso appartiene a questo ruolo. L'utente può quindi esplorare solo i dati specificati dai filtri di riga del ruolo. In questo scenario, si specifica che i dipendenti del reparto vendite possono solo visualizzare i dati relativi alle vendite Internet per i territori di vendita di cui sono membri.  
  
Le attività specifiche per questo scenario di modello tabulare Adventure Works, non necessariamente applicabili a uno scenario reale verranno identificate come tali. Ogni attività include informazioni aggiuntive che descrivono lo scopo dell'attività.  
  
Tempo previsto per il completamento della lezione: **30 minuti**  
  
## <a name="prerequisites"></a>Prerequisiti  
L'argomento di questa lezione supplementare fa parte di un'esercitazione sulla creazione di modelli tabulari, con lezioni che è consigliabile completare nell'ordine indicato. Prima di eseguire le attività in questa lezione supplementare, è necessario avere completato tutte le lezioni precedenti.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Aggiungere la tabella DimSalesTerritory al progetto di modello tabulare AW Internet Sales  
Per implementare la sicurezza dinamica per questo scenario di Adventure Works, è necessario aggiungere due tabelle al modello. La prima tabella da aggiungere è DimSalesTerritory (come Sales Territory) dallo stesso database AdventureWorksDW. In seguito si applicherà un filtro di riga alla tabella SalesTerritory che definisce i dati specifici che l'utente connesso può visualizzare.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Per aggiungere la tabella DimSalesTerritory  
  
1.  In Esplora modelli tabulari espandere **Origini dati**, fare clic con il pulsante destro del mouse sulla connessione e quindi scegliere **Importa nuove tabelle**.  

    Se viene visualizzata la finestra di dialogo Credenziali rappresentazione digitare le credenziali di rappresentazione usate nella lezione 2: Aggiungere dati.
  
2.  Nello strumento di navigazione selezionare la tabella **DimSalesTerritory** e quindi fare clic su **OK**.    
  
3.  Nell'Editor di query fare clic sulla query **DimSalesTerritory** e quindi rimuovere la colonna **SalesTerritoryAlternateKey**.  
  
7.  Fare clic su **Importa**.  
  
    La nuova tabella verrà aggiunta all'area di lavoro del modello. Gli oggetti e i dati dalla tabella di origine DimSalesTerritory vengono quindi importati nel modello tabulare AW Internet Sales.  
  
9. Dopo aver completato l'importazione della tabella fare clic su **Chiudi**.  

## <a name="add-a-table-with-user-name-data"></a>Aggiungere una tabella con i dati dei nomi utente  
La tabella DimEmployee nel database di esempio AdventureWorksDW contiene utenti del dominio AdventureWorks. Questo nomi utente, tuttavia, non esistono nel proprio ambiente. È necessario quindi creare una tabella nel modello che contenga un piccolo campione (almeno tre) di utenti reali dell'organizzazione. Questi utenti verranno poi aggiunti come membri al nuovo ruolo. Non servono le password per i nomi utente del campione, ma sono necessari nomi utente di Windows effettivi del proprio dominio.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Per aggiungere una tabella EmployeeSecurity  
  
1.  Aprire Microsoft Excel per creare un foglio di lavoro.  
  
2.  Copiare la tabella seguente, inclusa la riga di intestazione e quindi incollarla nel foglio di lavoro.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Sostituire nome, cognome e dominio\nomeutente con i nomi e gli ID di accesso dei tre utenti dell'organizzazione. Inserire lo stesso utente nelle prime due righe, in corrispondenza di EmployeeId 1, in modo da indicare che l'utente appartiene a più di un territorio di vendita. Lasciare invariati i campi EmployeeId e SalesTerritoryId.  
  
4.  Salvare il foglio di lavoro come **SampleEmployee**.  
  
5.  Nel foglio di lavoro selezionare tutte le celle con i dati dei dipendenti, incluse le intestazioni, quindi fare clic con il pulsante destro del mouse sui dati selezionati e scegliere **Copia**.  
  
6.  In SSDT scegliere **Incolla** dal menu **Modifica**.  
  
    Se il comando Incolla è disattivato, fare clic in qualsiasi colonna di qualsiasi tabella nella finestra di progettazione dei modelli e riprovare.  
  
7.  Nella finestra di dialogo **Anteprima Incolla** digitare **EmployeeSecurity** in **Nome tabella**.  
  
8.  In **Dati da incollare** verificare che i dati includano tutti i dati degli utenti e le intestazioni dal foglio di lavoro SampleEmployee.  
  
9. Verificare che l'opzione **Usa la prima riga per le intestazioni di colonna** sia selezionata e quindi fare clic su **Ok**.  
  
    Verrà creata una nuova tabella denominata EmployeeSecurity con i dati dei dipendenti copiati dal foglio di lavoro SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Creare relazioni tra le tabelle FactInternetSales, DimGeography e DimSalesTerritory  
Le tabelle FactInternetSales, DimGeography e DimSalesTerritory contengono tutte una colonna in comune, SalesTerritoryId. La colonna SalesTerritoryId nella tabella DimSalesTerritory contiene i valori con un ID diverso per ogni territorio di vendita.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Per creare relazioni tra le tabelle FactInternetSales, DimGeography e DimSalesTerritory  
  
1.  Nella vista diagramma, nella tabella **DimGeography** fare clic sulla colonna **SalesTerritoryId** e tenere premuto, trascinare il cursore sulla colonna **SalesTerritoryId** nella tabella **DimSalesTerritory** e quindi rilasciare.  
  
2.  Nella tabella **FactInternetSales** fare clic sulla colonna **SalesTerritoryId** e tenere premuto, trascinare il cursore sulla colonna **SalesTerritoryId** nella tabella **DimSalesTerritory** e quindi rilasciare.  
  
    Si noti che la proprietà Attiva per questa relazione è False, ovvero è inattiva. La tabella FactInternetSales, infatti, include già un'altra relazione attiva.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Nascondere la tabella EmployeeSecurity dalle applicazioni client  
In questa attività si nasconderà la tabella EmployeeSecurity, impedendone la visualizzazione nell'elenco dei campi di un'applicazione client. Tenere presente che nascondere una tabella non significa proteggerla. Gli utenti possono comunque eseguire query per recuperare dati dalla tabella EmployeeSecurity, se sanno come farlo. Per proteggere i dati della tabella EmployeeSecurity, impedendo agli utenti di poter eseguire query sui relativi dati, si applicherà un filtro in un'attività successiva.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Per nascondere la tabella EmployeeSecurity dalle applicazioni client  
  
-   Nella finestra di progettazione dei modelli, in vista diagramma, fare clic con il pulsante destro del mouse sull'intestazione della tabella **Employee** e quindi scegliere **Nascondi a strumenti client**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Creare un ruolo utente Sales Employees by Territory  
In questa attività si creerà un ruolo utente. Questo ruolo include un filtro di riga che definisce le righe della tabella DimSalesTerritory che sono visibili agli utenti. Il filtro viene quindi applicato nella direzione della relazione uno-a-molti a tutte le altre tabelle correlate a DimSalesTerritory. È anche possibile applicare un filtro che protegge l'intera tabella EmployeeSecurity impedendo l'esecuzione di query a qualsiasi utente membro del ruolo.  
  
> [!NOTE]  
> Il ruolo Sales Employees by Territory creato in questa lezione limita i membri consentendo loro di visualizzare solo i dati sulle vendite per il territorio a cui appartengono o di eseguire query su tali dati. Se si aggiunge un utente come membro al ruolo Sales Employees by Territory e tale utente esiste anche come membro di uno dei ruoli creati nella [Lezione 11: Creare ruoli](../tutorials/aas-lesson-11-create-roles.md), si ottiene una combinazione di autorizzazioni. Quando un utente è membro di più ruoli, le autorizzazioni e i filtri di riga definiti per ogni ruolo sono cumulativi. Questo significa che l'utente dispone delle autorizzazioni maggiori determinate dalla combinazione dei ruoli.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Per creare un ruolo utente Sales Employees by Territory  
  
1.  In SSDT fare clic sul menu **Modello** e quindi su **Ruoli**.  
  
2.  In **Gestione ruoli** fare clic su **Nuovo**.  
  
    Un nuovo ruolo con l'autorizzazione Nessuna verrà aggiunto all'elenco.  
  
3.  Fare clic sul nuovo ruolo e quindi nella colonna **Nome** rinominare il ruolo in **Sales Employees by Territory**.  
  
4.  Nella colonna **Autorizzazioni** fare clic nell'elenco a discesa e quindi selezionare l'autorizzazione **Lettura**.  
  
5.  Fare clic sulla scheda **Membri** e quindi fare clic su **Aggiungi**.  
  
6.  Nella finestra di dialogo **Seleziona utente o gruppo**, in **Immettere il nome dell'oggetto da selezionare**, digitare il primo nome utente di esempio usato durante la creazione della tabella EmployeeSecurity. Fare clic su **Controlla nomi** per verificare se il nome utente è valido e quindi fare clic su **OK**.  
  
    Ripetere questo passaggio, aggiungendo gli altri nomi utente di esempio usati durante la creazione della tabella EmployeeSecurity.  
  
7.  Fare clic sulla scheda **Filtri di riga**.  
  
8.  Per la tabella **EmployeeSecurity**, nella colonna **Filtro DAX** digitare la formula seguente:  
  
    ```
      =FALSE()  
    ```
  
    Questa formula specifica che tutte le colonne si risolvono nella condizione booleana False e che nessuna colonna della tabella EmployeeSecurity può essere sottoposta a query da un membro del ruolo utente Sales Employees by Territory.  
  
9. Per la tabella **DimSalesTerritory** digitare la formula seguente:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    In questa formula la funzione LOOKUPVALUE restituisce tutti i valori per la colonna DimEmployeeSecurity[SalesTerritoryId], in cui EmployeeSecurity[LoginId] corrisponde al nome utente di Windows connesso ed EmployeeSecurity[SalesTerritoryId] è uguale a DimSalesTerritory[SalesTerritoryId].  
  
    Il set di ID di territori di vendita restituiti da LOOKUPVALUE viene quindi usato per limitare le righe visualizzate nella tabella DimSalesTerritory. Vengono visualizzate solo le righe in cui il valore SalesTerritoryID per la riga è incluso nel set di ID restituito dalla funzione LOOKUPVALUE.  
  
10. In Gestione ruoli fare clic su **OK**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Testare il ruolo utente Sales Employees by Territory  
In questa attività si userà la funzionalità Analizza in Excel in SSDT per verificare l'efficacia del ruolo utente Sales Employees by Territory. Uno dei nomi utente aggiunti alla tabella EmployeeSecurity verrà specificato come membro del ruolo. Questo nome utente verrà quindi usato come nome utente effettivo nella connessione creata tra Excel e il modello.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Per testare il ruolo utente Sales Employees by Territory  
  
1.  In SSDT fare clic sul menu **Modello** e quindi su **Analizza in Excel**.  
  
2.  Nella finestra di dialogo **Analizza in Excel**, in **Specificare il nome utente o il ruolo da utilizzare per la connessione al modello**, selezionare **Altro utente di Windows** e quindi fare clic su **Sfoglia**.  
  
3.  Nella finestra di dialogo **Seleziona utente o gruppo**, in **Immettere il nome dell'oggetto da selezionare** digitare uno dei nomi utente inclusi nella tabella EmployeeSecurity e quindi fare clic su **Controlla nomi**.  
  
4.  Fare clic su **OK** per chiudere la finestra di dialogo **Seleziona utente o gruppo** e quindi fare clic su **OK** per chiudere la finestra di dialogo **Analizza in Excel**.  
  
    Excel verrà aperto con una nuova cartella di lavoro. Viene creata automaticamente una tabella pivot. L'elenco dei campi della tabella pivot include la maggior parte dei campi dati disponibili nel nuovo modello.  
  
    La tabella EmployeeSecurity non è visibile nell'elenco Campi tabella pivot, perché è stata nascosta dagli strumenti client in un'attività precedente.  
  
5.  Nell'elenco **Campi**, in **∑ Internet Sales** (misure), selezionare la misura **InternetTotalSales**. La misura verrà immessa nei campi **Valori**.  
  
6.  Selezionare la colonna **SalesTerritoryId** dalla tabella **DimSalesTerritory**. La colonna verrà immessa nei campi **Etichette di riga**.  
  
    Si noti che i dati relativi alle vendite Internet vengono visualizzati solo per l'unica area a cui appartiene il nome utente effettivo usato. Se si seleziona un'altra colonna, ad esempio City nella tabella DimGeography come campo etichetta di riga, vengono visualizzate solo le città del territorio di vendita a cui appartiene l'utente effettivo.  
  
    Questo utente non può visualizzare o eseguire query sui dati delle vendite Internet per territori diversi da quello che a cui appartiene. Questa restrizione dipende dal fatto che il filtro di riga definito per la tabella DimSalesTerritory, nel ruolo utente Sales Employees by Territory, protegge tutti i dati correlati ad altri territori di vendita.  
  
## <a name="see-also"></a>Vedere anche  
[USERNAME Function (DAX) (Funzione DAX USERNAME)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE Function (DAX) (Funzione DAX LOOKUPVALUE)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA Function (DAX) (Funzione DAX CUSTOMDATA)](https://msdn.microsoft.com/library/hh213140.aspx)  