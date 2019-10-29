# 脚本打包+上传fir

```text
#!/bin/bash 
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8

echo '打包脚本开始'

#工程路径
project_path=XXXXXX


#打包模式 Debug/Release
if [ $ServerType = TEST ];then
echo '*** 选择服务器环境：测试环境，调试环境：release ***' 
development_mode=Release_Test
server_name=测试
elif [ $ServerType = PRODUCTION ];then
echo '*** 选择服务器环境：生产环境，调试环境：release ***' 
development_mode=Release_Production
server_name=生产
elif [ $ServerType = DEVELOP ];then
echo '*** 选择服务器环境：开发环境，调试环境：release ***' 
development_mode=Release_Dev
server_name=开发
elif [ $ServerType = DEBUG_TEST ];then
echo '*** 选择服务器环境：开发环境，调试环境：debug ***' 
development_mode=Debug_Test
server_name=测试
else
echo '*** 缺省服务器环境：测试环境，调试环境：release ***' 
development_mode=Release_Dev
server_name=测试
fi


if [ $ServerType = DEBUG_TEST ];then
echo '*** 导入debug环境依赖静态库，导入debug环境打包配置 ***' 
#打包配置文件名
archive_plist_name=ExportOptionsDev.plist
debug_name=debug
certificate_name=development
else 
echo '*** 导入release环境依赖静态库，导入release环境打包配置 ***' 
#打包配置文件名
archive_plist_name=ExportOptionsADHOC.plist
debug_name=release
certificate_name=adhoc
fi

#工程名
project_name=XXXXXX

#scheme名
scheme_name=XXXXXX

#导出.ipa文件所在路径
exportFilePath=archive

#Info.plist位置
appInfoplistPath=${project_path}/${project_name}/Info.plist

#版本号
#bundleShortVersion=$(/usr/libexec/PlistBuddy -c "print CFBundleShortVersionString" ${appInfoplistPath})

#ipa路径
#ipaPath="${project_name}_${bundleShortVersion}_$(date +"%Y%m%d%H%M%S")_${ServerType}"
ipaPath="${project_name}_$(date +"%Y%m%d%H%M%S")_${ServerType}"

# echo '*** 正在 清理工程 ***'
# xcodebuild \
# -configuration ${project_path}/${development_mode} -quiet || exit
# echo '*** 清理完成 ***'

echo '*** 正在 安装依赖 ***'
cd ${project_path}
pod update
pod install --verbose --no-repo-update 
cd ..
echo '*** 安装依赖 完成 ***'

echo '*** 正在编译工程 For '${development_mode}
xcodebuild \
archive \
-workspace ${project_path}/${project_name}.xcworkspace \
-scheme ${scheme_name} \
-configuration ${development_mode} \
-archivePath ${exportFilePath}/${ipaPath}/${project_name}.xcarchive \
-destination generic/platform=iOS -quiet || exit
echo '*** 编译完成 ***'

echo '*** 正在打包 ***'
xcodebuild \
-exportArchive -archivePath ${exportFilePath}/${ipaPath}/${project_name}.xcarchive \
-configuration ${development_mode} \
-exportPath ${exportFilePath}/${ipaPath} \
-exportOptionsPlist ${archive_plist_name} \
-quiet || exit
echo '*** 打包完成 ***'

echo '*** 正在上传到Fir ***'
fir publish ${exportFilePath}/${ipaPath}/${project_name}.ipa -T 8b1xxxxxxxxxxxxxxxxxxxxxxx5a -c ${server_name}服务器_${debug_name}环境_${certificate_name}证书_Jenkins打包
echo '*** 成功上传到Fir ***'

echo '打包脚本结束'
```

