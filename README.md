# `lyrebird` binaries for arm/arm64 devices 

I used to copy `lyrebird.[arm/arm64].upx` files to router internal storage so it can be found as webtunnel transport plugin when openwrt boots. But it seems nand flash life cycle is even affected by repeatedly *read*ing "large" files (compared to the size of the storage, e.g., 128MB). 

The alternative would be hosting the files here and `curl` or `wget` them to `/tmp` then decompress the `*.tgz`.
