
```shell
# new branch
git checkout -b clone1

# make directories
mkdir -p clone1/{dictionary,update}

# generate sed script. Note that "clone1" is used as salt for a reproducable generated md5 to stand in as new sys_id
find xml/ -name '*.xml' -exec cat '{}' ';' | perl -MDigest::MD5 -ne 'm#<sys_id>([a-fA-F0-9]{32})</sys_id>#&& print "s/".$1."/".Digest::MD5::md5_hex("clone1".$1)."/g\n" ;' > clone1.sed 
echo "s/x_dete_emb/x_det_clone1/g" >> clone1.sed 


# copy the files over to new dest filename after passing through string replacement.
for file in $(find xml -type f );
do
   dest=$(echo ${file/xml/clone1} | sed -f clone1.sed) ;
   sed -f clone1.sed <$file > $dest ;
done

# change the path
sed -i 's/^path=.*/path=clone1/' sn_source_control.properties

# 
git add sn_source_control.properties clone1

git commit -m "initialize clone1 app from parent in xml."

git push origin clone1 


```
