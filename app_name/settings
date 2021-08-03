from django.test.signals import setting_changed


DEFAULTS = {}


class AppSettings:
    """
    A settings object that allows thj app settings to be accessed as
    properties. For example:
        from app_name.settings import app_settings
        print(app_settings.DEFAULT_NAME)
    """
    def __init__(self, user_settings=None, defaults=None):
        if user_settings:
            self._user_settings = self.__check_user_settings(user_settings)
        self.defaults = defaults or DEFAULTS
        self._cached_attrs = set()

    def __getattr__(self, attr):
        if attr not in self.defaults:
            raise AttributeError("Invalid App setting: '%s'" % attr)

        try:
            # Check if present in user settings
            val = self.user_settings[attr]
        except KeyError:
            # Fall back to defaults
            val = self.defaults[attr]

        # Cache the result
        self._cached_attrs.add(attr)
        setattr(self, attr, val)
        return val

    def reload(self):
        for attr in self._cached_attrs:
            delattr(self, attr)
        self._cached_attrs.clear()


app_settings = AppSettings(None, DEFAULTS)


def reload_app_settings(*args, **kwargs):
    setting = kwargs['setting']
    if setting == 'VERSIONING':
        app_settings.reload()


setting_changed.connect(reload_app_settings)
