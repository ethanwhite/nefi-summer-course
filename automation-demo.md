## Forecast Code

```r
gflu = read.csv("http://www.google.org/flutrends/about/data/flu/us/data.txt",skip=11)
y = ts(gflu$Massachusetts)
arima_model = arima(y, order = c(3, 0, 1))
forecast = predict(arima_model, n.ahead = 10)

jpeg("forecast_plot.jpg")
plot(y,type='l',ylab="Flu Index",lwd=2,xlim=c(540, 640),ylim=c(0, 4000))
lines(forecast$pred, col = 'blue', lwd=2)
dev.off()

predictions = data.frame(time = time(forecast$pred),
                         prediction = forecast$pred,
                         prediction = forecast$se)
write.csv(predictions, "predictions.csv", row.names = FALSE)
```

* Save as `forecast.R`

## Local Repetition

### Mac/Linux

On OS X and Linux you use `cron` to automatically run commands on a schedule:

```bash
crontab -e
m h d m dow /home/ethan/forecast.R
26 2 * * * /home/ethan/forecast.R
```

To change or remove just run `crontab -e` again.

### Windows

* **Start** (Windows key)
* **Task Scheduler**
* **Create Basic Task** (follow defaults)
* **Program/script** `Rscript`
* **Arguments** `/home/ethan/forecast.R`
* Show task in **Active Tasks**
* To change or delete double click task
* **Delete

## Remote Repetition

* Add `forecast.R` to GitHub
* Now set up to run whenever anythin in the repo is changed and put
  the updated results into the repo

### Connect GitHub to Travis for Continuous Integration/Deployment

1. GitHub
2. Make a new Repo w/README
3. https://travis-ci.com & login
4. Activate GitHub Apps -> Select Repo
5. GitHub -> Setting -> Dev Settings -> PAT
6. Add w/repo permissions (be careful with security)
7. Copy token
8. Travis -> More Options -> Settings
9. Environmental Variable -> name: GITHUB_TOKEN value: paste

### Give CI instructions on what to do

* First tell it what packages we need
* Create `install.R`
* We don't need any so we'll install `cowsay` as an example

```r
install.packages("cowsay")
```

* Next we need to tell the system what to do
* Do this using a yml file that provides metadata describing what we want
* Create `.travis.yml`

```yml
language: r
cache: packages

install:
  - Rscript -e "print('skipping standard install')"
  
script:
  - Rscript forecast.R

deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN 
  keep_history: true
  target_branch: master
```

* Show code running on Travis
* Add cron job -> branch: master timing: daily
* Show image and forecasts in GitHub

## Binder/Containers

* Add `runtime.txt` to repo

```
r-2019-08-01
```

* Copy repo url
* Go to https://mybinder.org
* Paste in url and click **Launch**
* *This will take a while so it's a time for questions*
  * There are [faster ways to setup binder for R](https://github.com/karthik/rstudio2019/blob/master/binder-notes.md)
* **New** -> **RStudio**
* Open `forecast.R`
* Run

* To integrate this with automation we use docker containers
  to run code that we want to rerun on a regular basis
* Walk through portalPredictions `.travis.yml`
    * https://github.com/weecology/portalPredictions/blob/master/.travis.yml
* And we can do similar things if we're working with a more complex system like OpenWhisk
