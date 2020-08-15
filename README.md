# sscheduler-slots

Flexible scheduler to find free time slots in the schedule of a resource (which could be a person, a meeting room, a car, etc...)

**sscheduler** can also intersect the availability of multiple resources in order to find the time slots at which all the resources are available.

## Installation

```bash
npm install @leooxx/sscheduler-slots
```

## Basic usage

As an example, let's say that we want to book a 1 hour appointment with a doctor in the month of february considering that:

  * We can only book on weekdays from *9AM* to *5PM*

  * We can't book between noon and *1PM* (lunch time !)

  * The doctor is on vacation the week of the *20th*

  * There are already two one-hour appointments booked on *February 1st* at *1PM* and *2PM*

```javascript
import {Scheduler} from '@leooxx/sscheduler-slots';

const scheduler = new Scheduler();
const availability = scheduler.getAvailability({
    from: '2017-02-01',
    to: '2017-03-01',
    duration: 60,
    interval: 60,
    schedule: {
        weekdays: {
            slots: [
                { from: '09:00', to: '12:00' },
                { from: '14:00', to: '18:00' }
            ],
            unavailability: [
                { from: '12:00', to: '13:00' }
            ]
        },
        unavailability: [
            // two different types of unavailability structure
            { from: '2017-02-20 00:00', to: '2017-02-27 00:00' },
            { date: '2017-02-15', from: '12:00', to: '13:00' }
        ],
        allocated: [
          { from: '2017-02-01 13:00' , duration: 60 },
          { from: '2017-02-01 14:00' , duration: 60 }
        ]
    }
});
```

The returned value is a structure that looks like the following:

```js
{
  '2017-02-01': [
    { time: '09:00', available: true },
    { time: '10:00', available: true }
    // ...
  ]
  // ...
}
```

# Options

The possible options for the **getAvailability** function are:

<details>
 <summary>from (required)</summary>
 The start date for which we want to get availability times
</details>

<details>
 <summary>to (required)</summary>
 The end date for which we want to get availability times (exclusive)
</details>

<details>
 <summary>interval (required)</summary>
 The interval (in minutes) of the returned availability times.
 For example, a value of 15 would returns availability times such as *10:00*, *10:15*, *10:30*, *10:45*, etc..
</details>

<details>
 <summary>duration (required)</summary>
 The duration (in minutes) for which we need the resource.
</details>

<details>
 <summary>schedule (required)</summary>
 The schedule of the resource for each day of the week.

 Example:
 ```js
 {
   monday: {
     from: '09:00',
     to: '17:00',
     unavailability: [
       { from: '12:00', to: '13:00' }
     ]
   },
   custom_schedule: [
     { "date": "2017-01-23", "from": "12:00", "to": "17:00" },
   ]
 }
 ```
</details>

## Schedule intersection

Using the same example as before, let's say that we also need to book a room for our appointment.

So, we need to intersect the doctor's availability times with the room's availability times, considering that:

  * We can only book the room on weekdays from *8AM* to *8PM*

  * The room is out of service from *February 6th* to *February 16th*

```javascript
import {Scheduler} from '@ssense/sscheduler';

const scheduler = new Scheduler();
const availability = scheduler.getIntersection({
    from: '2017-02-01',
    to: '2017-03-01',
    duration: 60,
    interval: 60,
    schedules: [
        // The doctor's schedule
        {
            weekdays: {
                from: '09:00', to: '17:00',
                unavailability: [
                    { from: '12:00', to: '13:00' }
                ]
            },
            unavailability: [
                { from: '2017-02-20 00:00', to: '2017-02-27 00:00' }
            ],
            allocated: [
              { from: '2017-02-01 13:00' , duration: 60 },
              { from: '2017-02-01 14:00' , duration: 60 }
            ]
        },

        // The room's schedule
        {
            weekdays: {
                from: '08:00', to: '20:00',
            },
            unavailability: [
                { from: '2017-02-06 00:00', to: '2017-02-16 00:00' }
            ]
        }
    ]
});
```

## Authors

* **Mickael Burguet** - *Senior Developer* - [rundef](http://rundef.com)
* **Léo POITOUT** - *Tourist Developer*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
