# Maintainer: Bill Ruddock <https://github.com/biinari>
# Contributor: Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Contributor: Marcello "mererghost" Rocha <https://github.com/mereghost>
# Refactored by Blaž "Speed" Hrastnik <https://github.com/archSeer>

_pkgname=elasticsearch
pkgname=elasticsearch5
pkgver=5.6.10
pkgrel=1
pkgdesc="Distributed RESTful search engine built on top of Lucene"
arch=('any')
url="https://www.elastic.co/products/elasticsearch"
license=('APACHE')
depends=('java-runtime-headless=8' 'systemd')
provides=("elasticsearch=$pkgver")
install='elasticsearch5.install'
source=(
  "https://artifacts.elastic.co/downloads/$_pkgname/$_pkgname-$pkgver.tar.gz"
  elasticsearch5.service
  elasticsearch5@.service
  elasticsearch5-sysctl.conf
  elasticsearch5-user.conf
  elasticsearch5-tmpfile.conf
  elasticsearch5.default
)
sha256sums=('4944c4102b0848d9e94daa50f3b57aa464250563d4ae53d639f19936e394dcd0'
            '0f999f90f180109dcd9bc880519dee9da88ddd5819329c05682d10b4e6ac0394'
            '5adcd11c1b23b21e3578dc598e5afc3b78bdbf7b860219b71497a9fabb82d594'
            'b3feb1e9c7e7ce6b33cea6c727728ed700332aae942ca475c3bcc1d56b9f113c'
            '815f6a39db6f54bb40750c382ffbdc298d2c4c187ee8ea7e2f855923e2ff354b'
            '19fb0e711354d014473dadb0cf37117ab7557887443177de0d091f5991a9376c'
            'bb74e5fb8bc28f2125e015395ab05bea117b72bfc6dadbca827694b362ee0bf8')

backup=('etc/elasticsearch5/elasticsearch.yml'
        'etc/elasticsearch5/log4j2.properties'
        'etc/elasticsearch5/jvm.options'
        'etc/default/elasticsearch5')

prepare() {
  cd "$srcdir"/$_pkgname-$pkgver

  for script in elasticsearch{,-plugin,-translog}; do
    sed -e 's|^ES_HOME=.*dirname.*|ES_HOME=/usr/share/elasticsearch5|' \
        -e '/^ES_HOME=.*pwd/d' \
        -e 's|$ES_HOME/config|/etc/elasticsearch5|' \
        -i bin/$script
  done

  sed -re 's;#\s*(path\.conf:).*$;\1 /etc/elasticsearch5;' \
    -e '0,/#\s*(path\.data:).*$/s;;\1 /var/lib/elasticsearch5;' \
    -e 's;#\s*(path\.work:).*$;\1 /tmp/elasticsearch5;' \
    -e 's;#\s*(path\.logs:).*$;\1 /var/log/elasticsearch5;' \
    -i config/elasticsearch.yml
}

package() {
  cd "$pkgdir"
  install -dm750 etc/elasticsearch5/scripts
  install -dm755 usr/share/elasticsearch5/plugins
  install -dm755 var/lib/elasticsearch5
  install -dm755 var/log/elasticsearch5

  install -Dm644 "$srcdir"/elasticsearch5.service usr/lib/systemd/system/elasticsearch5.service
  install -Dm644 "$srcdir"/elasticsearch5@.service usr/lib/systemd/system/elasticsearch5@.service
  install -Dm644 "$srcdir"/elasticsearch5-user.conf usr/lib/sysusers.d/elasticsearch5.conf
  install -Dm644 "$srcdir"/elasticsearch5-tmpfile.conf usr/lib/tmpfiles.d/elasticsearch5.conf
  install -Dm644 "$srcdir"/elasticsearch5-sysctl.conf usr/lib/sysctl.d/elasticsearch5.conf
  install -Dm644 "$srcdir"/elasticsearch5.default etc/default/elasticsearch5

  cd "$srcdir"/$_pkgname-$pkgver
  cp -R lib modules "$pkgdir"/usr/share/elasticsearch5/

	cd config
	for conf in *; do
		install -Dm644 "$conf" "$pkgdir/etc/elasticsearch5/$conf"
	done
	cd ..

  install -Dm755 bin/elasticsearch "$pkgdir"/usr/bin/elasticsearch5
  install -Dm755 bin/elasticsearch-plugin "$pkgdir"/usr/bin/elasticsearch5-plugin
  install -Dm755 bin/elasticsearch-translog "$pkgdir"/usr/bin/elasticsearch5-translog
  install -Dm644 bin/elasticsearch.in.sh "$pkgdir"/usr/share/elasticsearch5/bin/elasticsearch.in.sh
}
