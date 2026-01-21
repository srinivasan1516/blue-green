pipeline {
agent any


environment {
BLUE_DIR = '/var/www/blue'
GREEN_DIR = '/var/www/green'
ACTIVE_FILE = '/var/www/active_color'
}


stages {


stage('Checkout Code') {
steps {
git branch: 'main', url: 'https://github.com/srinivasan1516/blue-green.git'
}
}


stage('Detect Active Version') {
steps {
sh '''
if [ ! -f $ACTIVE_FILE ]; then
echo blue > $ACTIVE_FILE
fi
'''
}
}


stage('Deploy to Inactive Color') {
steps {
sh '''
ACTIVE=$(cat $ACTIVE_FILE)
if [ "$ACTIVE" = "blue" ]; then
TARGET=$GREEN_DIR
NEW=green
else
TARGET=$BLUE_DIR
NEW=blue
fi


rm -rf $TARGET/*
cp -r * $TARGET/
echo $NEW > $ACTIVE_FILE
'''
}
}


stage('Switch Traffic') {
steps {
sh '''
ACTIVE=$(cat $ACTIVE_FILE)
if [ "$ACTIVE" = "blue" ]; then
ROOT="/var/www/blue"
else
ROOT="/var/www/green"
fi


sudo sed -i "s|root .*;|root $ROOT;|" /etc/nginx/conf.d/bluegreen.conf
sudo systemctl reload nginx
'''
}
}
}
}
