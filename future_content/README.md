# Future content

Maybe record some exercises with http://showterm.io/

1. Fork all the things: Java buildpack
2. Tool: cf-pancake
3. Tool: cf-tmate (cf-ssh)
4. https://blog.starkandwayne.com/2014/10/31/public-and-private-microservices-on-the-same-cloud-foundry/
4. make a note of below with app chapter

```
lnguyen @DrNic might need your thoughts on some stuff, it turns out that atk server runs a bunch of little servers when it tries to do ‘data scientist stuff’
we can open up other ports on an app can we?

rkoster No you can't but you can deploy other apps

lnguyen thats gonna be a problem
 im counting 5 ‘ports’ that are creating

DrNic @lnguyen I thought it ran the little servers over on the hadoop machines?

lnguyen i thought so too

DrNic So we need to convert it into running them on CF instead of DIY?

lnguyen zoom? maybe easier to explain things via voice
DrNic breakfast

lnguyen kk
 let me know when you can zoom

wayneeseguin woo, so that is a good thing to watch out for with apps you want to convert for running on CF
@qanx maybe make a note of that with app chapter
```


### "What version of Cloud Foundry am I using?"

Can view PWS versions [here](https://network.pivotal.io/products/pivotal-cf). According to Dr Nic, PWS 1.3.3 = CF v189.

> Recommend discussing "what version of CF am I using" since different CF providers may use their own versioning system on top of the CF version. (As seen with PWS and CF.)

From Dr Nic (Kato 11/6):

>bosh-lite running cf-release from HEAD:
```
cat ~/.cf/config.json
{
  "ConfigVersion": 3,
  "Target": "https://api.10.244.0.34.xip.io",
  "ApiVersion": "2.17.0",
...
```
It's not easy/obvious how to determine what src is being used in any given CF you're targeting
but ApiVersion is one indicator
Take the ApiVersion, look for it in the release notes

Can see versions of API in [release notes](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/v191).

>Login to [IBM BlueMix](http://www-01.ibm.com/software/bluemix/), [PWS](http://run.pivotal.io/), etc
[Anynines](http://www.anynines.com/)
they'll all like it if we reference them in the post and the tweet
not in a negative way
just "so which version of CF is running and what features does it have?"
![](https://in.kato.im/c45f320cd8798474cb99afcfd054d69e749849fa8fae709bd370894e557f7083/clip.png)


So focus on:

* [IBM BlueMix](http://www-01.ibm.com/software/bluemix/)
* [PWS](http://run.pivotal.io/) - 1.3.3.0 = v???
* [Anynines](http://www.anynines.com/)
* [TryCF](https://trycf.starkandwayne.com) - v170
