# ⓻ Metrics: JWT Pizza Service

🔑 **Key points**

- Modify your CI pipeline to provide access to Grafana from the JWT Pizza Service.
- Record specific metrics from the service.
- Create a dashboard to visualize the metrics.
- Use Curl to simulate real traffic.

---

![course overview](../sharedImages/courseOverview.png)

It is time to add metrics observability to the jwt-pizza-service code. In your fork of the Pizza Service code use what you learned about [Grafana metrics](../grafanaMetrics/grafanaMetrics.md) to create visualizations that demonstrate the following:

1. HTTP requests by method/minute
1. Active users
1. Authentication attempts/minute
   1. Successful
   1. Failed
1. CPU usage percentage
1. Memory usage percentage
1. Pizzas
   1. Sold/minute
   1. Revenue/minute
   1. Creation latency
   1. Creation failures

## Modifying the application code

You are going to have to modify the `jwt-pizza-service` code in order to add observability. You want to keep modifications to the development team's work to a minimum. If you change things too much, you are probably going to have merge problems when they update the application and you have to merge your fork.

Try to use design patterns and principles such as middleware and modularity to isolate your changes as much as possible.

## Getting started

This assignment should feel similar to the exercises you have already completed. However, there are a lot of different metrics that are required and it will take some time to figure out how to gather the metrics, instrument the code, and finally create the visualizations. Here are some suggestions about how to get started.

### Add Grafana credentials to config.js

Modify your config.js file to contain the Grafana credentials. You can then reference these configuration settings just like the application uses the database settings.

```js
  metrics: {
    source: 'jwt-pizza-service',
    userId: 1,
    url: '',
    apiKey: '',
  }
```

### Modify CI pipeline

Because you are added new configuration to the JWT Service, you will need to also enhance your GitHub Actions workflow to have the new metrics configuration fields. You must also add secrets for the metrics METRICS_USER_ID, METRICS_URL, and METRICS_API_KEY.

Without this your CI pipeline will fail because of missing references from your new metrics code when your tests run.

```yml
- name: Write config file
  run: |
    echo "module.exports = {
      jwtSecret: '${{ secrets.JWT_SECRET }}',
      db: {
        connection: {
          host: '127.0.0.1',
          user: 'root',
          password: 'tempdbpassword',
          database: 'pizza',
          connectTimeout: 60000,
        },
        listPerPage: 10,
      },
      factory: {
        url: 'https://pizza-factory.cs329.click',
        apiKey: '${{ secrets.FACTORY_API_KEY }}',
      },
      metrics: {
        source: 'jwt_pizza_service',
        userId: ${{ secrets.METRICS_USER_ID }},
        url: '${{ secrets.METRICS_URL }}',
        apiKey: '${{ secrets.METRICS_API_KEY }}',
      },            
    };" > src/config.js
```

### Create metrics.js

Create a file named `metrics.js`. Use this file to for all the code necessary to interact with Grafana. This may be somewhat similar to what you created in the [Grafana Metrics instruction](../grafanaMetrics/grafanaMetrics.md). However, it will need to be more complex than what was presented in the instruction because it will have to supply metrics for more than just http requests.

### Add request metrics code

Modify your Express application routers to report on the request related metrics. If you expose an Express middleware function from your metrics class, this will give you a good start on providing metrics generated by any incoming HTTP requests.

```js
app.use(metrics.requestTracker);
```

### Add system metrics code

Modify `metrics.js` to periodically report on the system metrics. You can get metrics from the operating system in node.js with the `os` module as demonstrated in the following code.

```js
const os = require('os');

function getCpuUsagePercentage() {
  const cpuUsage = os.loadavg()[0] / os.cpus().length;
  return cpuUsage.toFixed(2) * 100;
}

function getMemoryUsagePercentage() {
  const totalMemory = os.totalmem();
  const freeMemory = os.freemem();
  const usedMemory = totalMemory - freeMemory;
  const memoryUsage = (usedMemory / totalMemory) * 100;
  return memoryUsage.toFixed(2);
}
```

### Add purchase metrics code

The metrics that track your purchases will require that you calculate information whenever a purchase is made. This includes how long it took for the Pizza Factory to respond to the request to make a pizza, how many were made, how much it cost, and if it was successful.

### Simulating traffic

You will need some traffic to your website in order to demonstrate that the visualizations are working. The easiest way to do this, is to follow the [Simulating traffic](../simulatingTraffic/simulatingTraffic.md) instruction.

## ☑ Assignment

In order to demonstrate your mastery of the concepts for this deliverable, complete the following.

1. Modify your fork of the `jwt-pizza-service` to generate the required metrics and store them in your Grafana Cloud account.
1. Create visualizations on your Grafana Cloud `Pizza Dashboard` to display all of the required metrics.
1. Export a copy of your dashboard and save it to your fork of the `jwt-pizza-service` repository in a directory named `grafana`.
   1. On the Grafana Cloud console, navigate to your dashboard.
   1. Press the `Share` button.
   1. Press the `Export` tab and `Save to file`.
   1. Name the file `grafana/deliverable7dashboard.json`
1. Commit and push your changes so that they are running in your production environment.

Once this is all working you should have something like this:

![Metric dashboard](metricDashboard.png)

Get the public URL for your dashboard and submit it to the Canvas assignment. This should look something like this:

```txt
https://youraccounthere.grafana.net/public-dashboards/29305se9fsacc66a21fa91899b75734
```

### Rubric

| Percent | Item                                                                                      |
| ------- | ----------------------------------------------------------------------------------------- |
| 10%     | Strong GitHub commit history that documents your work in your fork of `jwt-pizza-service` |
| 30%     | Storing all required metrics in Grafana Cloud Prometheus data store                       |
| 60%     | Visualizing all required metrics in Grafana Cloud dashboard                               |

**Congratulations!** You have provided significant observability for your JWT Pizza Service. Time to go celebrate. I'm thinking tacos 🌮.
