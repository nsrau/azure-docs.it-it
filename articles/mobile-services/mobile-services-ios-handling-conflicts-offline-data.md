<properties
	pageTitle="Gestire i conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili (iOS) | Mobile Dev Center"
	description="Informazioni sull'uso di Servizi mobili di Azure per gestire i conflitti durante la sincronizzazione dei dati offline nell'applicazione iOS."
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="krisragh;donnam"/>


# Gestione dei conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

Questo argomento illustra come sincronizzare e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure. Questa esercitazione si basa sull'esercitazione [Introduzione ai dati non in linea].

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.


## Scaricare progetto iOS

Per questa esercitazione, scaricare [un progetto Xcode aggiornato da Github](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS). È stato utilizzato il progetto Xcode dalla fine dell’esercitazione [Introduzione ai dati Offline] come punto di avvio, e poi è stato aggiornato in modo da consentire la modifica degli elementi. Sono state aggiunte anche le classi e i metodi di supporto in modo da poter aggiungere un gestore del conflitto nella sezione successiva.

Al termine di questa esercitazione, se si esegue questa applicazione su due telefoni, si modifica lo stesso elemento su entrambi i telefoni localmente, e si invia nuovamente il push al server, si consentirà all'utente di scegliere in ogni telefono la versione da mantenere: * mantenere la versione client (che sostituisce la versione sul server), * mantenere la versione del server (che aggiorna la tabella locale client) , o *non mantenere nessuna versione (Annulla push e lascia l'operazione in sospeso).

Ora, aggiungiamo il gestore del conflitto per abilitare questa funzionalità.

## <a name="add-conflict-handling"></a>Aggiungere il gestore dei conflitti al controller di visualizzazione dell'elenco attività

1. In **QSTodoListViewController.m** modificare **viewDidLoad**. Sostituire la chiamata a **defaultService** con una chiamata a **defaultServiceWithDelegate**:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. In **QSTodoListViewController.h** aggiungere **&lt;MSSyncContextDelegate&gt;** alla dichiarazione dell'interfaccia, in modo da implementare il protocollo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Aggiungere l'istruzione di importazione seguente nella parte superiore di **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Infine, si aggiungeranno le due operazioni seguenti a **QSTodoListViewController.m** per usare la classe helper e richiedere all'utente di riconciliare il conflitto usando uno di tre metodi.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

## <a name="test-app"></a>Testare l'app

Si procederà ora al test dell'app con i conflitti. Modificare lo stesso elemento in due diverse istanze dell'app in esecuzione nello stesso momento o usando l'app e un client REST.

Eseguire il movimento di aggiornamento nelle istanze delle app mediante trascinamento dall'alto. A questo punto verrà visualizzata una richiesta di risoluzione del conflitto:

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Introduzione ai dati Offline]: mobile-services-ios-get-started-offline-data.md
[Introduzione ai dati non in linea]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!---HONumber=Sept15_HO4-->