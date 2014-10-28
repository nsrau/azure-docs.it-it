È infine necessario aggiornare lo script registrato nell'operazione di inserimento nella tabella TodoItem per l'invio di notifiche.

1.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][]

2.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
        // Define a payload for the Windows Store toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
            '<binding template="ToastText01">  <text id="1">' +
            item.text + '</text></binding></visual></toast>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.wns.send(null, payload, 'wns/toast', {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });
        }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutte le app di Windows Store registrate dopo la corretta esecuzione dell'inserimento.

  []: ./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png
