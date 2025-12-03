# `lyrebird` binaries for arm/arm64 devices 

I used to copy `lyrebird.[arm/arm64].upx` file to router internal storage so it can be found as webtunnel transport plugin when openwrt boots. But it seems nand flash life cycle is even affected by repeatedly *read*ing "large" files (compared to the size of the storage, e.g., 128MB). 

The alternative would be hosting the files here and `curl` or `wget` them to `/tmp` then decompress the `*.tgz`.

## How to use 

Add the following e.g., to `/etc/rc.local`

``` bash
# /etc/rc.local
if [ $(uname -m) == 'aarch64' ] ; then 
  ARM='arm64'
  echo 'openwrt is running on arm64'
else
  ARM='arm'
  echo 'openwrt is running on armv7l'
fi

RELEASE='0.7.0'
LYREBIRD_BIN_URL='https://github.com/h9j6k/lyrebird_arm/releases/download/v'$RELEASE'/lyrebird.'$ARM'.tgz'
GEOIP_URL='https://github.com/h9j6k/lyrebird_arm/releases/download/v'$RELEASE'/geoip.tgz'

if ! [ -f /tmp/lyrebird ] ; then  
  /etc/init.d/tor stop
  echo 'tor service stopped'

  echo 'lyrebird does not exist, download from github ...'
  wget -P /tmp/ $LYREBIRD_BIN_URL 
  wget -P /tmp/ $GEOIP_URL

  if [ -f /tmp/lyrebird.$ARM.tgz ] ; then
    # need to install tar beforehand 
    tar -xzvf /tmp/lyrebird.$ARM.tgz -C /tmp/
    rm -v /tmp/lyrebird.$ARM.tgz
  fi 
  if [ -f /tmp/geoip.tgz ] ; then 
    tar -xzvf /tmp/geoip.tgz -C /tmp/
    rm -v /tmp/geoip.tgz
  fi

  if [ -f /tmp/lyrebird.$ARM.upx ] ; then
    echo 'lyrebird downloaded, verifying ...'
    # add your fav checksum tool here
    mv -v /tmp/lyrebird.$ARM.upx /tmp/lyrebird 
    echo 'lyrebird verified, now start tor service'
    chown -hR tor:tor /tmp/geoip* /tmp/lyrebird
    /etc/init.d/tor start
  fi
else
  echo 'lyrebird exists, no need to download from github'
fi

exit 0
```
