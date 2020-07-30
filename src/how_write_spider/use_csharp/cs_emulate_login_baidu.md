# C#模拟登录百度首页

之前用过`C#`实现模拟登陆百度首页，且有2个版本及对应的更新版本。

具体过程详见：

* [【教程】模拟登陆网站 之 C#版（内含两种版本的完整的可运行的代码）](http://www.crifan.com/emulate_login_website_using_csharp/)

下面整理如下：

#### C#模拟登陆百度首页之纯内置库版

```cs
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
  
using System.Net;
using System.IO;
using System.Text.RegularExpressions;
using System.Web;
  
namespace emulateLoginBaidu
{
    public partial class frmEmulateLoginBaidu : Form
    {
        CookieCollection curCookies = null;
  
        bool gotCookieBaiduid, extractTokenValueOK, loginBaiduOk;
  
        public frmEmulateLoginBaidu()
        {
            InitializeComponent();
        }
  
        private void frmEmulateLoginBaidu_Load(object sender, EventArgs e)
        {
            //init
            curCookies = new CookieCollection();
            gotCookieBaiduid = false;
            extractTokenValueOK = false;
            loginBaiduOk = false;
        }
  
        /******************************************************************************
        functions in crifanLib.cs
        *******************************************************************************/
  
        //quote the input dict values
        //note: the return result for first para no '&'
        public string quoteParas(Dictionary<string, string> paras)
        {
            string quotedParas = "";
            bool isFirst = true;
            string val = "";
            foreach (string para in paras.Keys)
            {
                if (paras.TryGetValue(para, out val))
                {
                    if (isFirst)
                    {
                        isFirst = false;
                        quotedParas += para + "=" + HttpUtility.UrlPathEncode(val);
                    }
                    else
                    {
                        quotedParas += "&" + para + "=" + HttpUtility.UrlPathEncode(val);
                    }
                }
                else
                {
                    break;
                }
            }
  
            return quotedParas;
        }
  
        /******************************************************************************
        Demo emulate login baidu related functions
        *******************************************************************************/
  
        private void btnGetBaiduid_Click(object sender, EventArgs e)
        {
            //http://www.baidu.com/
            string baiduMainUrl = txbBaiduMainUrl.Text;
            //generate http request
            HttpWebRequest req = (HttpWebRequest)WebRequest.Create(baiduMainUrl);
  
            //add follow code to handle cookies
            req.CookieContainer = new CookieContainer();
            req.CookieContainer.Add(curCookies);
  
            req.Method = "GET";
            //use request to get response
            HttpWebResponse resp = (HttpWebResponse)req.GetResponse();
            txbGotBaiduid.Text = "";
            foreach (Cookie ck in resp.Cookies)
            {
                txbGotBaiduid.Text += "[" + ck.Name + "]=" + ck.Value;
                if (ck.Name == "BAIDUID")
                {
                    gotCookieBaiduid = true;
                }
            }
  
            if (gotCookieBaiduid)
            {
                //store cookies
                curCookies = resp.Cookies;
            }
            else
            {
                MessageBox.Show("错误：没有找到cookie BAIDUID ！");
            }
        }
  
        private void btnGetToken_Click(object sender, EventArgs e)
        {
            if (gotCookieBaiduid)
            {
                string getapiUrl = "https://passport.baidu.com/v2/api/?getapi&class=login&tpl=mn&tangram=true";
                HttpWebRequest req = (HttpWebRequest)WebRequest.Create(getapiUrl);
  
                //add previously got cookies
                req.CookieContainer = new CookieContainer();
                req.CookieContainer.Add(curCookies);
  
                req.Method = "GET";
                HttpWebResponse resp = (HttpWebResponse)req.GetResponse();
                StreamReader sr = new StreamReader(resp.GetResponseStream());
                string respHtml = sr.ReadToEnd();
  
                //bdPass.api.params.login_token='5ab690978812b0e7fbbe1bfc267b90b3';
                string tokenValP = @"bdPass\.api\.params\.login_token='(?<tokenVal>\w+)';";
                Match foundTokenVal = (new Regex(tokenValP)).Match(respHtml);
                if (foundTokenVal.Success)
                {
                    //extracted the token value
                    txbExtractedTokenVal.Text = foundTokenVal.Groups["tokenVal"].Value;
                    extractTokenValueOK = true;
                }
                else
                {
                    txbExtractedTokenVal.Text = "错误：没有找到token的值！";
                }
  
            }
            else
            {
                MessageBox.Show("错误：之前没有正确获得Cookie：BAIDUID ！");
            }
        }
  
        private void btnEmulateLoginBaidu_Click(object sender, EventArgs e)
        {
            if (gotCookieBaiduid && extractTokenValueOK)
            {
                string staticpage = "http://www.baidu.com/cache/user/html/jump.html";
                  
                //init post dict info
                Dictionary<string, string> postDict = new Dictionary<string, string>();
                //postDict.Add("ppui_logintime", "");
                postDict.Add("charset", "utf-8");
                //postDict.Add("codestring", "");
                postDict.Add("token", txbExtractedTokenVal.Text);
                postDict.Add("isPhone", "false");
                postDict.Add("index", "0");
                //postDict.Add("u", "");
                //postDict.Add("safeflg", "0");
                postDict.Add("staticpage", staticpage);
                postDict.Add("loginType", "1");
                postDict.Add("tpl", "mn");
                postDict.Add("callback", "parent.bdPass.api.login._postCallback");
                postDict.Add("username", txbBaiduUsername.Text);
                postDict.Add("password", txbBaiduPassword.Text);
                //postDict.Add("verifycode", "");
                postDict.Add("mem_pass", "on");
  
                string baiduMainLoginUrl = "https://passport.baidu.com/v2/api/?login";
                HttpWebRequest req = (HttpWebRequest)WebRequest.Create(baiduMainLoginUrl);
                //add cookie
                req.CookieContainer = new CookieContainer();
                req.CookieContainer.Add(curCookies);
                //set to POST
                req.Method = "POST";
                req.ContentType = "application/x-www-form-urlencoded";
                //prepare post data
                string postDataStr = quoteParas(postDict);
                byte[] postBytes = Encoding.UTF8.GetBytes(postDataStr);
                req.ContentLength = postBytes.Length;
                //send post data
                Stream postDataStream = req.GetRequestStream();
                postDataStream.Write(postBytes, 0, postBytes.Length);
                postDataStream.Close();
                //got response
                HttpWebResponse resp = (HttpWebResponse)req.GetResponse();
                //got returned html
                StreamReader sr = new StreamReader(resp.GetResponseStream());
                string loginBaiduRespHtml = sr.ReadToEnd();
  
                //check whether got all expected cookies
                Dictionary<string, bool> cookieCheckDict = new Dictionary<string, bool>();
                string[] cookiesNameList = {"BDUSS", "PTOKEN", "STOKEN", "SAVEUSERID"};
                foreach (String cookieToCheck in cookiesNameList)
                {
                    cookieCheckDict.Add(cookieToCheck, false);
                }
  
                foreach (Cookie singleCookie in resp.Cookies)
                {
                    if (cookieCheckDict.ContainsKey(singleCookie.Name))
                    {
                        cookieCheckDict[singleCookie.Name] = true;
                    }
                }
  
                bool allCookiesFound = true;
                foreach (bool foundCurCookie in cookieCheckDict.Values)
                {
                    allCookiesFound = allCookiesFound && foundCurCookie;
                }
  
  
                loginBaiduOk = allCookiesFound;
                if (loginBaiduOk)
                {
                    txbEmulateLoginResult.Text = "成功模拟登陆百度首页！";
                }
                else
                {
                    txbEmulateLoginResult.Text = "模拟登陆百度首页 失败！";
                    txbEmulateLoginResult.Text += Environment.NewLine + "所返回的Header信息为：";
                    txbEmulateLoginResult.Text += Environment.NewLine + resp.Headers.ToString();
                    txbEmulateLoginResult.Text += Environment.NewLine + Environment.NewLine;
                    txbEmulateLoginResult.Text += Environment.NewLine + "所返回的HTML源码为：";
                    txbEmulateLoginResult.Text += Environment.NewLine + loginBaiduRespHtml;
                }
            }
            else
            {
                MessageBox.Show("错误：没有正确获得Cookie BAIDUID 和/或 没有正确提取出token值！");
            }
        }
  
        private void lklEmulateLoginTutorialUrl_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            string emulateLoginTutorialUrl = "https://www.crifan.com/emulate_login_website_using_csharp";
            System.Diagnostics.Process.Start(emulateLoginTutorialUrl);
        }
  
        private void btnClearAll_Click(object sender, EventArgs e)
        {
            curCookies = new CookieCollection();
            gotCookieBaiduid = false;
            extractTokenValueOK = false;
            loginBaiduOk = false;
  
            txbGotBaiduid.Text = "";
            txbExtractedTokenVal.Text = "";
  
            txbBaiduUsername.Text = "";
            txbBaiduPassword.Text = "";
            txbEmulateLoginResult.Text = "";
        }
    }
}
```

模拟登录成功的效果截图：

![emulate_login_baidu_cs_ok](../../assets/img/emulate_login_baidu_cs_ok.png)

完整的C#代码（VS2010项目）下载：

* [emulateLoginBaidu_csharp_2012-11-07.7z](http://book.crifan.com/books/crawl_your_data_spider_technology/website/assets/file/emulateLoginBaidu_csharp_2012-11-07.7z)

以及后续针对`.NET 4.0`优化版本：

* 模拟百度登陆 独立完整代码版本 .NET 4.0
    * [emulateLoginBaidu_csharp_independentCodeVersion_2013-09-11.7z](http://book.crifan.com/books/crawl_your_data_spider_technology/website/assets/file/emulateLoginBaidu_csharp_independentCodeVersion_2013-09-11.7z)

#### C#模拟登陆百度首页之crifanLib.cs版

完整代码：

```cs
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
  
using System.Net;
using System.IO;
using System.Text.RegularExpressions;
using System.Web;
  
namespace emulateLoginBaidu
{
    public partial class frmEmulateLoginBaidu : Form
    {
        CookieCollection curCookies = null;
  
        bool gotCookieBaiduid, extractTokenValueOK, loginBaiduOk;
  
        public frmEmulateLoginBaidu()
        {
            InitializeComponent();
        }
  
        private void frmEmulateLoginBaidu_Load(object sender, EventArgs e)
        {
            this.AcceptButton = this.btnEmulateLoginBaidu;
  
            //init for crifanLib.cs
            curCookies = new CookieCollection();
  
            //init for demo login
            gotCookieBaiduid = false;
            extractTokenValueOK = false;
            loginBaiduOk = false;
        }
  
        /******************************************************************************
        functions in crifanLib.cs
        Online browser: http://code.google.com/p/crifanlib/source/browse/trunk/csharp/crifanLib.cs
        Download:       http://code.google.com/p/crifanlib/
        *******************************************************************************/
  
        //quote the input dict values
        //note: the return result for first para no '&'
        public string quoteParas(Dictionary<string, string> paras)
        {
            string quotedParas = "";
            bool isFirst = true;
            string val = "";
            foreach (string para in paras.Keys)
            {
                if (paras.TryGetValue(para, out val))
                {
                    if (isFirst)
                    {
                        isFirst = false;
                        quotedParas += para + "=" + HttpUtility.UrlPathEncode(val);
                    }
                    else
                    {
                        quotedParas += "&" + para + "=" + HttpUtility.UrlPathEncode(val);
                    }
                }
                else
                {
                    break;
                }
            }
  
            return quotedParas;
        }
  
  
        /*********************************************************************/
        /* cookie */
        /*********************************************************************/
  
        //add a single cookie to cookies, if already exist, update its value
        public void addCookieToCookies(Cookie toAdd, ref CookieCollection cookies, bool overwriteDomain)
        {
            bool found = false;
  
            if (cookies.Count > 0)
            {
                foreach (Cookie originalCookie in cookies)
                {
                    if (originalCookie.Name == toAdd.Name)
                    {
                        // !!! for different domain, cookie is not same,
                        // so should not set the cookie value here while their domains is not same
                        // only if it explictly need overwrite domain
                        if ((originalCookie.Domain == toAdd.Domain) ||
                            ((originalCookie.Domain != toAdd.Domain) && overwriteDomain))
                        {
                            //here can not force convert CookieCollection to HttpCookieCollection,
                            //then use .remove to remove this cookie then add
                            // so no good way to copy all field value
                            originalCookie.Value = toAdd.Value;
  
                            originalCookie.Domain = toAdd.Domain;
  
                            originalCookie.Expires = toAdd.Expires;
                            originalCookie.Version = toAdd.Version;
                            originalCookie.Path = toAdd.Path;
  
                            //following fields seems should not change
                            //originalCookie.HttpOnly = toAdd.HttpOnly;
                            //originalCookie.Secure = toAdd.Secure;
  
                            found = true;
                            break;
                        }
                    }
                }
            }
  
            if (!found)
            {
                if (toAdd.Domain != "")
                {
                    // if add the null domain, will lead to follow req.CookieContainer.Add(cookies) failed !!!
                    cookies.Add(toAdd);
                }
            }
  
        }//addCookieToCookies
  
        //add singel cookie to cookies, default no overwrite domain
        public void addCookieToCookies(Cookie toAdd, ref CookieCollection cookies)
        {
            addCookieToCookies(toAdd, ref cookies, false);
        }
  
        //check whether the cookies contains the ckToCheck cookie
        //support:
        //ckTocheck is Cookie/string
        //cookies is Cookie/string/CookieCollection/string[]
        public bool isContainCookie(object ckToCheck, object cookies)
        {
            bool isContain = false;
  
            if ((ckToCheck != null) && (cookies != null))
            {
                string ckName = "";
                Type type = ckToCheck.GetType();
  
                //string typeStr = ckType.ToString();
  
                //if (ckType.FullName == "System.string")
                if (type.Name.ToLower() == "string")
                {
                    ckName = (string)ckToCheck;
                }
                else if (type.Name == "Cookie")
                {
                    ckName = ((Cookie)ckToCheck).Name;
                }
  
                if (ckName != "")
                {
                    type = cookies.GetType();
  
                    // is single Cookie
                    if (type.Name == "Cookie")
                    {
                        if (ckName == ((Cookie)cookies).Name)
                        {
                            isContain = true;
                        }
                    }
                    // is CookieCollection
                    else if (type.Name == "CookieCollection")
                    {
                        foreach (Cookie ck in (CookieCollection)cookies)
                        {
                            if (ckName == ck.Name)
                            {
                                isContain = true;
                                break;
                            }
                        }
                    }
                    // is single cookie name string
                    else if (type.Name.ToLower() == "string")
                    {
                        if (ckName == (string)cookies)
                        {
                            isContain = true;
                        }
                    }
                    // is cookie name string[]
                    else if (type.Name.ToLower() == "string[]")
                    {
                        foreach (string name in ((string[])cookies))
                        {
                            if (ckName == name)
                            {
                                isContain = true;
                                break;
                            }
                        }
                    }
                }
            }
  
            return isContain;
        }//isContainCookie
  
        // update cookiesToUpdate to localCookies
        // if omitUpdateCookies designated, then omit cookies of omitUpdateCookies in cookiesToUpdate
        public void updateLocalCookies(CookieCollection cookiesToUpdate, ref CookieCollection localCookies, object omitUpdateCookies)
        {
            if (cookiesToUpdate.Count > 0)
            {
                if (localCookies == null)
                {
                    localCookies = cookiesToUpdate;
                }
                else
                {
                    foreach (Cookie newCookie in cookiesToUpdate)
                    {
                        if (isContainCookie(newCookie, omitUpdateCookies))
                        {
                            // need omit process this
                        }
                        else
                        {
                            addCookieToCookies(newCookie, ref localCookies);
                        }
                    }
                }
            }
        }//updateLocalCookies
          
        //update cookiesToUpdate to localCookies
        public void updateLocalCookies(CookieCollection cookiesToUpdate, ref CookieCollection localCookies)
        {
            updateLocalCookies(cookiesToUpdate, ref localCookies, null);
        }
  
        /*********************************************************************/
        /* HTTP */
        /*********************************************************************/
  
        /* get url's response */
        public HttpWebResponse getUrlResponse(string url,
                                        Dictionary<string, string> headerDict,
                                        Dictionary<string, string> postDict,
                                        int timeout,
                                        string postDataStr)
        {
            //CookieCollection parsedCookies;
  
            HttpWebResponse resp = null;
  
            HttpWebRequest req = (HttpWebRequest)WebRequest.Create(url);
  
            req.AllowAutoRedirect = true;
            req.Accept = "*/*";
  
            //const string gAcceptLanguage = "en-US"; // zh-CN/en-US
            //req.Headers["Accept-Language"] = gAcceptLanguage;
  
            req.KeepAlive = true;
  
            //IE8
            //const string gUserAgent = "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; InfoPath.3; .NET4.0C; .NET4.0E";
            //IE9
            //const string gUserAgent = "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0)"; // x64
            const string gUserAgent = "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)"; // x86
            //Chrome
            //const string gUserAgent = "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/533.4 (KHTML, like Gecko) Chrome/5.0.375.99 Safari/533.4";
            //Mozilla Firefox
            //const string gUserAgent = "Mozilla/5.0 (Windows; U; Windows NT 6.1; rv:1.9.2.6) Gecko/20100625 Firefox/3.6.6";
            req.UserAgent = gUserAgent;
  
            req.Headers["Accept-Encoding"] = "gzip, deflate";
            req.AutomaticDecompression = DecompressionMethods.GZip;
  
            req.Proxy = null;
  
            if (timeout > 0)
            {
                req.Timeout = timeout;
            }
  
            if (curCookies != null)
            {
                req.CookieContainer = new CookieContainer();
                req.CookieContainer.PerDomainCapacity = 40; // following will exceed max default 20 cookie per domain
                req.CookieContainer.Add(curCookies);
            }
  
            if (headerDict != null)
            {
                foreach (string header in headerDict.Keys)
                {
                    string headerValue = "";
                    if (headerDict.TryGetValue(header, out headerValue))
                    {
                        // following are allow the caller overwrite the default header setting
                        if (header.ToLower() == "referer")
                        {
                            req.Referer = headerValue;
                        }
                        else if (header.ToLower() == "allowautoredirect")
                        {
                            bool isAllow = false;
                            if (bool.TryParse(headerValue, out isAllow))
                            {
                                req.AllowAutoRedirect = isAllow;
                            }
                        }
                        else if (header.ToLower() == "accept")
                        {
                            req.Accept = headerValue;
                        }
                        else if (header.ToLower() == "keepalive")
                        {
                            bool isKeepAlive = false;
                            if (bool.TryParse(headerValue, out isKeepAlive))
                            {
                                req.KeepAlive = isKeepAlive;
                            }
                        }
                        else if (header.ToLower() == "accept-language")
                        {
                            req.Headers["Accept-Language"] = headerValue;
                        }
                        else if (header.ToLower() == "useragent")
                        {
                            req.UserAgent = headerValue;
                        }
                        else
                        {
                            req.Headers[header] = headerValue;
                        }
                    }
                    else
                    {
                        break;
                    }
                }
            }
  
            if (postDict != null || postDataStr != "")
            {
                req.Method = "POST";
                req.ContentType = "application/x-www-form-urlencoded";
  
                if (postDict != null)
                {
                    postDataStr = quoteParas(postDict);
                }
  
                //byte[] postBytes = Encoding.GetEncoding("utf-8").GetBytes(postData);
                byte[] postBytes = Encoding.UTF8.GetBytes(postDataStr);
                req.ContentLength = postBytes.Length;
  
                Stream postDataStream = req.GetRequestStream();
                postDataStream.Write(postBytes, 0, postBytes.Length);
                postDataStream.Close();
            }
            else
            {
                req.Method = "GET";
            }
  
            //may timeout, has fixed in:
            //https://www.crifan.com/fixed_problem_sometime_httpwebrequest_getresponse_timeout/
            resp = (HttpWebResponse)req.GetResponse();
  
            updateLocalCookies(resp.Cookies, ref curCookies);
  
            return resp;
        }
  
        public HttpWebResponse getUrlResponse(string url,
                                    Dictionary<string, string> headerDict,
                                    Dictionary<string, string> postDict)
        {
            return getUrlResponse(url, headerDict, postDict, 0, "");
        }
  
        public HttpWebResponse getUrlResponse(string url)
        {
            return getUrlResponse(url, null, null, 0, "");
        }
  
        // valid charset:"GB18030"/"UTF-8", invliad:"UTF8"
        public string getUrlRespHtml(string url,
                                        Dictionary<string, string> headerDict,
                                        string charset,
                                        Dictionary<string, string> postDict,
                                        int timeout,
                                        string postDataStr)
        {
            string respHtml = "";
  
            //HttpWebResponse resp = getUrlResponse(url, headerDict, postDict, timeout);
            HttpWebResponse resp = getUrlResponse(url, headerDict, postDict, timeout, postDataStr);
  
            //long realRespLen = resp.ContentLength;
  
            StreamReader sr;
            if ((charset != null) && (charset != ""))
            {
                Encoding htmlEncoding = Encoding.GetEncoding(charset);
                sr = new StreamReader(resp.GetResponseStream(), htmlEncoding);
            }
            else
            {
                sr = new StreamReader(resp.GetResponseStream());
            }
            respHtml = sr.ReadToEnd();
  
            return respHtml;
        }
  
        public string getUrlRespHtml(string url, Dictionary<string, string> headerDict, string charset, Dictionary<string, string> postDict, string postDataStr)
        {
            return getUrlRespHtml(url, headerDict, charset, postDict, 0, postDataStr);
        }
  
        public string getUrlRespHtml(string url, Dictionary<string, string> headerDict, Dictionary<string, string> postDict)
        {
            return getUrlRespHtml(url, headerDict, "", postDict, "");
        }
  
        public string getUrlRespHtml(string url, Dictionary<string, string> headerDict)
        {
            return getUrlRespHtml(url, headerDict, null);
        }
  
        public string getUrlRespHtml(string url, string charset, int timeout)
        {
            return getUrlRespHtml(url, null, charset, null, timeout, "");
        }
  
        public string getUrlRespHtml(string url, string charset)
        {
            return getUrlRespHtml(url, charset, 0);
        }
  
        public string getUrlRespHtml(string url)
        {
            return getUrlRespHtml(url, "");
        }
  
  
        /******************************************************************************
        Demo emulate login baidu related functions
        *******************************************************************************/
  
        private void btnGetBaiduid_Click(object sender, EventArgs e)
        {
            //http://www.baidu.com/
            string baiduMainUrl = txbBaiduMainUrl.Text;
            HttpWebResponse resp = getUrlResponse(baiduMainUrl);
            txbGotBaiduid.Text = "";
            foreach (Cookie ck in resp.Cookies)
            {
                txbGotBaiduid.Text += "[" + ck.Name + "]=" + ck.Value;
                if (ck.Name == "BAIDUID")
                {
                    gotCookieBaiduid = true;
                }
            }
  
            if (gotCookieBaiduid)
            {
                //store cookies
                curCookies = resp.Cookies;
            }
            else
            {
                MessageBox.Show("错误：没有找到cookie BAIDUID ！");
            }
        }
  
        private void btnGetToken_Click(object sender, EventArgs e)
        {
            if (gotCookieBaiduid)
            {
                string getapiUrl = "https://passport.baidu.com/v2/api/?getapi&class=login&tpl=mn&tangram=true";
                string respHtml = getUrlRespHtml(getapiUrl);
  
                //bdPass.api.params.login_token='5ab690978812b0e7fbbe1bfc267b90b3';
                string tokenValP = @"bdPass\.api\.params\.login_token='(?<tokenVal>\w+)';";
                Match foundTokenVal = (new Regex(tokenValP)).Match(respHtml);
                if (foundTokenVal.Success)
                {
                    //extracted the token value
                    txbExtractedTokenVal.Text = foundTokenVal.Groups["tokenVal"].Value;
                    extractTokenValueOK = true;
                }
                else
                {
                    txbExtractedTokenVal.Text = "错误：没有找到token的值！";
                }
  
            }
            else
            {
                MessageBox.Show("错误：之前没有正确获得Cookie：BAIDUID ！");
            }
        }
  
        private void btnEmulateLoginBaidu_Click(object sender, EventArgs e)
        {
            if (gotCookieBaiduid && extractTokenValueOK)
            {
                string staticpage = "http://www.baidu.com/cache/user/html/jump.html";
                  
                //init post dict info
                Dictionary<string, string> postDict = new Dictionary<string, string>();
                //postDict.Add("ppui_logintime", "");
                postDict.Add("charset", "utf-8");
                //postDict.Add("codestring", "");
                postDict.Add("token", txbExtractedTokenVal.Text);
                postDict.Add("isPhone", "false");
                postDict.Add("index", "0");
                //postDict.Add("u", "");
                //postDict.Add("safeflg", "0");
                postDict.Add("staticpage", staticpage);
                postDict.Add("loginType", "1");
                postDict.Add("tpl", "mn");
                postDict.Add("callback", "parent.bdPass.api.login._postCallback");
                postDict.Add("username", txbBaiduUsername.Text);
                postDict.Add("password", txbBaiduPassword.Text);
                //postDict.Add("verifycode", "");
                postDict.Add("mem_pass", "on");
  
                string baiduMainLoginUrl = "https://passport.baidu.com/v2/api/?login";
                string loginBaiduRespHtml = getUrlRespHtml(baiduMainLoginUrl, null, postDict);
  
                //check whether got all expected cookies
                Dictionary<string, bool> cookieCheckDict = new Dictionary<string, bool>();
                string[] cookiesNameList = {"BDUSS", "PTOKEN", "STOKEN", "SAVEUSERID"};
                foreach (String cookieToCheck in cookiesNameList)
                {
                    cookieCheckDict.Add(cookieToCheck, false);
                }
  
                foreach (Cookie singleCookie in curCookies)
                {
                    if (cookieCheckDict.ContainsKey(singleCookie.Name))
                    {
                        cookieCheckDict[singleCookie.Name] = true;
                    }
                }
  
                bool allCookiesFound = true;
                foreach (bool foundCurCookie in cookieCheckDict.Values)
                {
                    allCookiesFound = allCookiesFound && foundCurCookie;
                }
  
  
                loginBaiduOk = allCookiesFound;
                if (loginBaiduOk)
                {
                    txbEmulateLoginResult.Text = "成功模拟登陆百度首页！";
                }
                else
                {
                    txbEmulateLoginResult.Text = "模拟登陆百度首页 失败！";
                    txbEmulateLoginResult.Text += Environment.NewLine + "所返回的HTML源码为：";
                    txbEmulateLoginResult.Text += Environment.NewLine + loginBaiduRespHtml;
                }
            }
            else
            {
                MessageBox.Show("错误：没有正确获得Cookie BAIDUID 和/或 没有正确提取出token值！");
            }
        }
  
        private void lklEmulateLoginTutorialUrl_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            string emulateLoginTutorialUrl = "https://www.crifan.com/emulate_login_website_using_csharp";
            System.Diagnostics.Process.Start(emulateLoginTutorialUrl);
        }
  
        private void btnClearAll_Click(object sender, EventArgs e)
        {
            curCookies = new CookieCollection();
            gotCookieBaiduid = false;
            extractTokenValueOK = false;
            loginBaiduOk = false;
  
            txbGotBaiduid.Text = "";
            txbExtractedTokenVal.Text = "";
  
            txbBaiduUsername.Text = "";
            txbBaiduPassword.Text = "";
            txbEmulateLoginResult.Text = "";
        }
  
    }
}
```

完整的C#代码（VS2010项目）下载：

* [emulateLoginBaidu_csharp_crifanLibVersion_2012-11-07.7z](http://book.crifan.com/books/crawl_your_data_spider_technology/website/assets/file/emulateLoginBaidu_csharp_crifanLibVersion_2012-11-07.7z)

以及后续针对`.NET 4.0`优化版本：

* 模拟百度登陆 （利用我自己的）crifanLib版本 .NET 4.0
    * [emulateLoginBaidu_csharp_crifanLibVersion_2013-09-11.7z](http://book.crifan.com/books/crawl_your_data_spider_technology/website/assets/file/emulateLoginBaidu_csharp_crifanLibVersion_2013-09-11.7z)
