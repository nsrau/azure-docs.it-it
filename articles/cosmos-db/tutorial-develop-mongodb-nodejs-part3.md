---
title: Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Compilare l'interfaccia utente con Angular
titleSuffix: Azure Cosmos DB
description: Parte 3 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: 462d0ea036ee50a3eea6812d87772bba9559125c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788583"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Compilare l'interfaccia utente con Angular

Questa esercitazione in più parti illustra come creare una nuova app scritta in Node.js con Express e Angular e quindi connetterla all'[account Cosmos configurato con l'API di Cosmos DB per MongoDB](mongodb-introduction.md).

La Parte 3 dell'esercitazione è basata sulla [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Creazione dell'interfaccia utente di Angular
> * Uso di CSS per configurare l'aspetto
> * Test dell'app in locale

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa parte dell'esercitazione, assicurarsi di avere completato le procedure illustrate nella [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) dell'esercitazione.

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) su GitHub.

## <a name="build-the-ui"></a>Creare l'interfaccia utente

1. In Visual Studio Code fare clic sul pulsante Arresta ![Pulsante Arresta in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png) per arrestare l'app Node.

2. Nel prompt dei comandi di Windows o nella finestra del terminale Mac immettere il comando seguente per generare un componente di tipo heroes. Nel codice g equivale a generare, c a componente, heroes al nome del componente e viene usata la struttura di file flat (--flat), in modo che non venga creata alcuna sottocartella.

    ```
    ng g c heroes --flat 
    ```

    La finestra del terminale mostra una conferma dei nuovi componenti.

    ![Installare il componente hero](./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png)

    È possibile esaminare i file creati e aggiornati. 

3. Nel riquadro **Esplora risorse** in Visual Studio Code passare alla nuova cartella **src\app** e aprire il nuovo file **heroes.component.ts** generato all'interno della cartella app. Questo file del componente TypeScript è stato creato dal comando precedente.

    > [!TIP]
    > Se la cartella app non viene visualizzata in Visual Studio Code, premere CMD + MAIUSC P in un computer Mac o CTRL + MAIUSC + P in un computer Windows per aprire il riquadro comandi e quindi digitare *Ricarica finestra* per rilevare la modifica di sistema.

4. Nella stessa cartella aprire il file **app.module.ts**. Come si può notare, è stato aggiunto `HeroesComponent` alle dichiarazioni alla riga 5 e lo stesso valore è stato importato anche nella riga 10.

    ![Aprire il file app-module.ts](./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png)

5. Tornare al file **heroes.component.html** e copiarlo nel codice. Il valore `<div>` indica il contenitore per l'intera pagina. All'interno del contenitore è disponibile un elenco di tutti gli elementi hero da creare. Quando vi si fa clic è quindi possibile selezionarne uno e modificarlo o eliminarlo nell'interfaccia utente. Il codice HTML include alcuni aspetti relativi allo stile, per evidenziare gli elementi selezionati. È disponibile anche un'area di modifica, che consente di aggiungere un nuovo elemento hero o modificarne uno esistente. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Quando il codice HTML è disponibile, è necessario aggiungerlo al file **heroes.component.ts**, in modo che sia possibile interagire con il modello. Il codice seguente aggiunge il modello al file del componente. È stato aggiunto un costruttore che recupera alcuni elementi hero e inizializza il componente del servizio hero per ottenere tutti i dati. Questo codice consente anche di aggiungere tutti i metodi necessari per la gestione di eventi nell'interfaccia utente. È possibile copiare il codice seguente sopra il codice esistente nel file **heroes.component.ts**. È previsto che vengano visualizzati errori nelle aree Hero e HeroService dal momento che i componenti corrispondenti non sono stati ancora importati; questo errore verrà risolto nella sezione successiva. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. In **Esplora risorse** aprire il file **app/app.module.ts** e aggiornare la sezione imports per aggiungere un'operazione di importazione per `FormsModule`. La sezione di importazione dovrebbe avere ora un aspetto simile al seguente:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. Nel file **app/app.module.ts** aggiungere un'operazione di importazione per il nuovo modulo FormsModule alla riga 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Uso di CSS per configurare l'aspetto

1. Nel riquadro Esplora risorse aprire il file **src/styles.scss**.

2. Copiare il codice seguente nel file **styles.scss**, sostituendo il contenuto esistente del file.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Salvare il file. 

## <a name="display-the-component"></a>Visualizzare il componente

Quando il componente è disponibile, è necessario fare in modo che venga visualizzato sullo schermo. Modificare i componenti predefiniti nel file **app.component.ts**.

1. Nel riquadro Esplora risorse aprire **/app/app.component.ts**, modificare il titolo in Heroes e quindi inserire il nome del componente creato nel file **heroes.components.ts** (app-heroes) in modo da fare riferimento al nuovo componente. Il contenuto del file dovrebbe avere ora un aspetto simile al seguente: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Il file **heroes.components.ts** include altri componenti a cui viene fatto riferimento, ad esempio il componente Hero, quindi è necessario creare anche quello. Nel prompt dei comandi dell'interfaccia della riga di comando di Angular usare il comando seguente per creare un modello hero e un file denominato **hero.ts**, in cui g equivale a generare, cl a classe e hero al nome della classe.

    ```bash
    ng g cl hero
    ```

3. Nel riquadro Esplora risorse aprire **src\app\hero.ts**. Nel file **hero.ts** sostituire il contenuto del file con il codice seguente, che consente di aggiungere una classe Hero con un ID, un nome e un elemento saying.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Tornare al file **heroes.components.ts**. Come si può notare, la riga `selectedHero: Hero;` (riga 10), `Hero` ha una sottolineatura rossa. 

5. Fare clic con il pulsante sinistro del mouse sul termine `Hero`. Visual Studio mostra un'icona a forma di lampadina a sinistra del blocco di codice. 

    ![Lampadina in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png)

6. Fare clic sulla lampadina e quindi su **Importa Hero da "/app/hero"** oppure su **Importa Hero da "./hero"**. Il messaggio cambia in base alla configurazione.

    Una nuova riga di codice viene visualizzata alla riga 2. Se la riga 2 fa riferimento ad /app/hero, modificarla in modo che faccia riferimento al file hero dalla cartella locale (./hero). La riga 2 dovrebbe risultare simile alla seguente:

   ```
   import { Hero } from "./hero";
   ``` 

    Il modello è ora disponibile, ma è ancora necessario creare il servizio.

## <a name="create-the-service"></a>Creare il servizio

1. Nel prompt dei comandi dell'interfaccia della riga di comando di Angular immettere il comando seguente per creare un servizio di tipo hero in **app.module.ts**, in cui g equivale a generare, s a servizio, hero al nome del servizio e -m all'inserimento in app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. In Visual Studio Code tornare al file **heroes.components.ts**. Si noti che la riga `constructor(private heroService: HeroService) {}` (riga 13), `HeroService` ha una sottolineatura rossa. Fare clic su `HeroService`. Verrà visualizzata l'icona a forma di lampadina a sinistra del blocco di codice. Fare clic sulla lampadina e quindi su **Importa HeroService da "./hero.service "** oppure su **Importa HeroService da "/app/hero.service"**.

    La selezione della lampadina consente di inserire una nuova riga di codice alla riga 2. Se la riga 2 fa riferimento alla cartella /app/hero.service, modificarla in modo che faccia riferimento al file hero dalla cartella locale (./hero.service). La riga 2 dovrebbe risultare simile alla seguente:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. In Visual Studio Code aprire il file **hero.service.ts** e copiarlo nel codice seguente, sostituendo il contenuto del file.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Questo codice usa la versione più recente di HttpClient offerta da Angular. Si tratta di un modulo che deve essere specificato dall'utente, quindi occorre eseguire subito questa procedura.

4. In Visual Studio Code aprire il file **app.module.ts** e importare il modulo HttpClientModule aggiornando la sezione di importazione in modo da includere HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. Nel file **app.module.ts** aggiungere l'istruzione import di HttpClientModule all'elenco di operazioni di importazione.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Salvare tutti i file in Visual Studio Code.

## <a name="build-the-app"></a>Compilare l'app

1. Al prompt dei comandi immettere il comando seguente per compilare l'applicazione Angular. 

    ```bash
    ng b
    ``` 

    Se si verificano problemi, la finestra del terminale mostra informazioni sui file da correggere. Al termine della compilazione, i file vengono inseriti nella cartella **dist**. È possibile esaminare i nuovi file nella cartella **dist**, se si vuole.

    Ora eseguire l'app.

2. In Visual Studio Code fare clic sul pulsante **Debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png) a sinistra, quindi sul pulsante **Avvia debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png).

3. Aprire un browser Internet e passare a **localhost:3000** per visualizzare l'app in esecuzione in locale.

     ![Applicazione Hero in esecuzione in locale](./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png)

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Creazione dell'interfaccia utente di Angular
> * Test dell'app in locale

È possibile passare alla parte successiva dell'esercitazione per creare un account Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Creare un account Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure](tutorial-develop-mongodb-nodejs-part4.md)
