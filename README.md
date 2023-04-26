# Velociraptor Artifacts

**PSList.VTLookup**

```name: PSList.VTLookup
description: |
   Combination of PSList with Virus Total reputation lookup using the Server Enrichment Artifact by Wes Lambert.

type: CLIENT

author: Chris Jones - CPIRT

parameters:
   - name: VTKey
     default: VTKey

sources:
  - precondition:
      SELECT OS From info() where OS = 'windows' or 'linux'

    query: |
        LET Key <= VTKey

        LET Results = SELECT Name,Pid,Ppid,Username,{
            Select Name FROM pslist(pid=Ppid)
        } AS ParentName,hash(path=Exe).SHA1 AS SHA1,
        CommandLine, Exe FROM pslist()

        SELECT *, {SELECT VTRating FROM Artifact.Server.Enrichment.Virustotal(VirustotalKey=VTKey,Hash=SHA1)} AS VTResults
        FROM foreach(row=Results)
```
