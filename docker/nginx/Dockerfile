FROM centos:latest
MAINTAINER https://github.com/CentOS/sig-cloud-instance-images

LABEL Vendor="CentOS" \
      License=GPLv2 \
      Version=2.4.6-40

ENV nginxversion="1.12.2-1" \
    os="centos" \
    osversion="7" \
    elversion="7_4"
    
#install nginx
RUN yum install -y wget openssl sed &&\
    yum -y autoremove &&\
    yum clean all &&\
    wget http://nginx.org/packages/$os/$osversion/x86_64/RPMS/nginx-$nginxversion.el$elversion.ngx.x86_64.rpm &&\
    rpm -iv nginx-$nginxversion.el$elversion.ngx.x86_64.rpm &&\
    sed -i '1i\
    daemon off;\
    ' /etc/nginx/nginx.conf && \
    rm -rf nginx*
    
# install supervisord

#config nginx
RUN rm -rf /etc/nginx/nginx.conf 
ADD nginx.conf /etc/nginx/


EXPOSE 80

# Simple startup script to avoid some issues observed with container restart
ADD run-httpd.sh /run-httpd.sh
RUN chmod -v +x /run-httpd.sh

CMD ["/run-httpd.sh"]