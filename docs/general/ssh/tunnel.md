# Creating a Tunnel

When not in CERN, to access resources which are unavailable outside CERN, you will
need to tunnel your computer's network through SSH. 

[Sournce](https://security.web.cern.ch/recommendations/en/ssh_tunneling.shtml).

## Linux

```bash
python3 -m pip install sshuttle
sshuttle --dns -vr <username>@lxtunnel.cern.ch 137.138.0.0/16 128.141.0.0/16 128.142.0.0/16 188.184.0.0/15 --python=python3
```
