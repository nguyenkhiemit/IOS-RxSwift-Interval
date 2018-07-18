
# IOS-RxSwift-Interval
Create repeat interval with RxSwift

Ok, now something more interesting. Let's create that interval operator that was used in previous examples.<br>

This is equivalent of actual implementation for dispatch queue schedulers <br>

        func myInterval(_ interval: TimeInterval) -> Observable<Int> {
            return Observable.create { observer in
                print("Subscribed")
                let timer = DispatchSource.makeTimerSource(queue: DispatchQueue.global())
                timer.scheduleRepeating(deadline: DispatchTime.now() + interval, interval: interval)

                let cancel = Disposables.create {
                    print("Disposed")
                    timer.cancel()
                }

                var next = 0
                timer.setEventHandler {
                    if cancel.isDisposed {
                        return
                    }
                    observer.on(.next(next))
                    next += 1
                }
                timer.resume()

                return cancel
            }
        }
        
        
 How to use ? <br>
 
    let counter = myInterval(0.1)

    print("Started ----")

    let subscription = counter
        .subscribe(onNext: { n in
            print(n)
        })


    Thread.sleep(forTimeInterval: 0.5)

    subscription.dispose()

    print("Ended ----")
    
This will print : 

    Started ----
    Subscribed
    0
    1
    2
    3
    4
    Disposed
    Ended ----
